# Day55 多线程 Web 服务器 2

> 目标：从零实现一个固定大小的线程池（ThreadPool），让 Web 服务器能并发处理请求，并支持优雅停机——退出时等正在处理的请求跑完。

## 今天要解决什么

Day54 我们发现：

- 单线程：慢请求堵死一切
- 每连接一线程：并发是并发了，但不能无限开线程

**线程池（thread pool）**就是标准答案：预先开好 N 个线程待命；来请求了放进任务队列，空闲线程取出来执行；线程数固定，不会被突发流量打死。

生活比喻：线程池像餐厅的固定服务员团队。客人来了排号，空闲的服务员去接；不会来一个客人就新招一个服务员（太贵），也不会让所有客人排队等同一个服务员（太慢）。

## 对外接口长什么样

我们想让 main 用起来像这样：

```rust
let pool = ThreadPool::new(4);              // 开 4 个线程
for stream in listener.incoming() {
    let stream = stream.unwrap();
    pool.execute(|| {                        // 像 thread::spawn 一样丢任务
        handle_connection(stream);
    });
}
println!("服务器关闭");   // 离开作用域，pool drop，优雅停机
```

`execute` 接收一个闭包（任务），约束和 `thread::spawn` 一样：`F: FnOnce() + Send + 'static`。

## 内部结构

```rust
use std::sync::{mpsc, Arc, Mutex};
use std::thread;

// 任务就是一个装箱的闭包：能跑一次、能跨线程、拥有自己的数据
type Job = Box<dyn FnOnce() + Send + 'static>;

// 通道消息：要么来新任务，要么通知 worker 退出
enum Message {
    NewJob(Job),
    Terminate,
}

pub struct ThreadPool {
    workers: Vec<Worker>,                          // 固定 N 个工人
    sender: mpsc::Sender<Message>,                 // 任务队列的发送端
}

struct Worker {
    id: usize,
    thread: Option<thread::JoinHandle<()>>,        // Option 是为了停机时能 take 出来
}
```

几个设计决策解释：

- **为什么用 `Box<dyn FnOnce()>`**：每个任务类型不同（闭包大小不一），要装箱成 trait object 才能放进同一个队列
- **为什么通道消息是枚举 `Message`**：除了发任务，还要能发“停机”信号
- **为什么 `thread` 包 `Option`**：`join()` 会消费掉线程句柄，而 Rust 不允许从 `&mut self` 里 move 出来；用 `Option::take()` 把 `Some` 里的值移出来，留下 `None`

## 创建线程池

```rust
impl ThreadPool {
    /// 创建一个大小为 size 的线程池
    pub fn new(size: usize) -> ThreadPool {
        assert!(size > 0, "线程池大小必须大于 0");

        let (sender, receiver) = mpsc::channel();
        // receiver 要被 N 个 worker 共享，所以用 Arc<Mutex<...>> 包起来
        let receiver = Arc::new(Mutex::new(receiver));

        let mut workers = Vec::with_capacity(size);
        for id in 0..size {
            workers.push(Worker::new(id, Arc::clone(&receiver)));
        }

        ThreadPool { workers, sender }
    }

    /// 提交一个任务
    pub fn execute<F>(&self, f: F)
    where
        F: FnOnce() + Send + 'static,
    {
        let job = Box::new(f);
        self.sender.send(Message::NewJob(job)).unwrap();
    }
}
```

关键点：

- `mpsc::channel` 是“多生产者、单消费者”——但我们有 N 个 worker 都要从同一个 receiver 取任务。解法是把 receiver 包进 `Arc<Mutex<...>>`：`Arc` 让所有权共享给 N 个 worker，`Mutex` 保证同一时刻只有一个 worker 在 `recv()`
- `execute` 把闭包装箱成 `Job`，通过通道发出去

## Worker：待命线程

```rust
impl Worker {
    fn new(id: usize, receiver: Arc<Mutex<mpsc::Receiver<Message>>>) -> Worker {
        let thread = thread::spawn(move || loop {
            // 加锁拿 receiver，recv() 阻塞等任务
            let message = receiver.lock().unwrap().recv().unwrap();

            match message {
                Message::NewJob(job) => {
                    println!("Worker {} 拿到一个任务", id);
                    job();   // 执行任务
                }
                Message::Terminate => {
                    println!("Worker {} 收到停机信号，退出", id);
                    break;   // 跳出 loop，线程结束
                }
            }
        });

        Worker { id, thread: Some(thread) }
    }
}
```

这里有个**很容易踩的坑**：为什么要把 `let message = ...` 单独写一行，再 `match` 里 `job()`？

因为 `receiver.lock()` 返回的 `MutexGuard` 会一直活到作用域结束。如果写成：

```rust
// 错误示范：锁会一直持有到 job() 跑完
match receiver.lock().unwrap().recv().unwrap() {
    Message::NewJob(job) => job(),   // job() 执行期间还拿着锁！
    ...
}
```

那 `job()` 跑的时候锁没释放，其他 worker 根本拿不到任务，并发就退化成串行。把 `let message = ...` 放在单独一行，`MutexGuard` 在这行结束时就被丢弃（作用域结束），锁释放了，然后才 `job()`。

## 优雅停机：Drop

直接 Ctrl-C 会强杀正在处理请求的线程。我们希望：pool 离开作用域时，先通知所有 worker 停手，再 `join` 等它们跑完手里的活。

```rust
impl Drop for ThreadPool {
    fn drop(&mut self) {
        // 第一步：发 N 条 Terminate，每个 worker 都能收到一条
        for _ in &self.workers {
            self.sender.send(Message::Terminate).unwrap();
        }

        // 第二步：逐个 join，等线程真正结束
        for worker in &mut self.workers {
            println!("关闭 Worker {}", worker.id);
            if let Some(thread) = worker.thread.take() {
                thread.join().unwrap();
            }
        }
    }
}
```

**为什么必须分两个循环？** 如果边发 Terminate 边 join，可能出现：我发的 Terminate 被别的 worker 抢走了，而我正在 join 的那个 worker 还在忙——它永远收不到 Terminate，我就死等。先发够 N 条，保证每个 worker 都有一份退出信号，再统一 join。

## 接到 Web 服务器里

```rust
use std::net::TcpListener;

fn main() {
    let listener = TcpListener::bind("127.0.0.1:7878").unwrap();
    let pool = ThreadPool::new(4);

    // take(2) 演示用：只处理 2 个请求就停机，触发 drop
    for stream in listener.incoming().take(2) {
        let stream = stream.unwrap();
        pool.execute(|| {
            handle_connection(stream);
        });
    }

    println!("服务器关闭");
    // pool 在这里离开作用域，自动 drop → 优雅停机
}

fn handle_connection(mut stream: std::net::TcpStream) {
    // ... 和 Day54 一样，读请求、拼响应、写回 ...
}
```

## 今日小结

- 线程池 = 固定 N 个预创建线程 + 一个任务队列（mpsc channel）
- `ThreadPool::new(size)` 创建池；`pool.execute(闭包)` 提交任务
- 多 worker 共享 receiver：`Arc<Mutex<mpsc::Receiver<Message>>>`
- 通道消息升级为 `Message::NewJob` / `Message::Terminate` 枚举
- worker 线程 `loop { recv → 执行/退出 }`；注意锁要在 `job()` 之前释放
- `Drop` 里先发 N 条 Terminate 再逐个 join，实现优雅停机

## 动手任务

1. 把 `ThreadPool` 完整敲进项目，接 Day54 的 `handle_connection`，用 `cargo run` 跑起来。
2. 用 `take(2)` 演示停机：处理 2 个请求后观察终端打印的“关闭 Worker”日志，确认 4 个线程都被干净回收。
3. 把 `take(2)` 去掉（真正无限服务），开多个浏览器标签同时访问 `/sleep`，确认它们并发处理而不是排队。

> 提示：`Vec::with_capacity(size)` 预分配容量避免后续扩容；`assert!(size > 0)` 防止传 0 导致死锁（没人接收任务，发任务时会阻塞）。

**配合阅读**：[官方书·20.2 改用多线程](https://rustwiki.org/zh-CN/book/ch20-02-multithreaded.html) · [官方书·20.3 优雅停机与清理](https://rustwiki.org/zh-CN/book/ch20-03-graceful-shutdown-and-cleanup.html)
