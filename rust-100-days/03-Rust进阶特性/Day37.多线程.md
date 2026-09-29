# Day37 多线程

> 目标：学会用 `std::thread` 创建线程、用 `join` 等待它结束、用 `move` 把数据搬进线程，并体会 Rust 怎么在编译期就挡住数据竞争。

## 为什么要多线程

一个程序如果从头到尾一件事一件事串行做，像单窗口办事——窗口前一个人没办完，后面全等着。多线程就是多开几个窗口，不同的活儿同时跑。现代 CPU 都是多核，多线程才能真的把多核用起来。

Rust 标准库提供的是 **1:1 线程**：一个 Rust 线程对应一个操作系统原生线程。创建线程很便宜，但也别滥用。

## 创建线程：thread::spawn

用 `thread::spawn` 传一个闭包，闭包里就是新线程要执行的代码：

```rust
use std::thread;
use std::time::Duration;

fn main() {
    // 派一个子线程去跑这段循环
    thread::spawn(|| {
        for i in 1..10 {
            println!("子线程: {}", i);
            thread::sleep(Duration::from_millis(1)); // 睡 1 毫秒，模拟干活
        }
    });

    // 主线程自己也跑一段
    for i in 1..5 {
        println!("主线程: {}", i);
        thread::sleep(Duration::from_millis(1));
    }
    // ⚠️ 注意：主线程一旦结束，整个程序就退出，子线程会被强行杀掉
}
```

你会看到「主线程」和「子线程」的打印交错出现——它们确实在并发跑。但上面这个版本有个隐患：主线程循环 5 次就结束了，子线程可能还没跑完 9 次就被腰斩。

## join：等子线程跑完

`thread::spawn` 返回一个 `JoinHandle`（句柄）。调它的 `.join()` 会**阻塞当前线程**，直到这个子线程结束：

```rust
use std::thread;
use std::time::Duration;

fn main() {
    let handle = thread::spawn(|| {
        for i in 1..10 {
            println!("子线程: {}", i);
            thread::sleep(Duration::from_millis(1));
        }
    });

    for i in 1..5 {
        println!("主线程: {}", i);
        thread::sleep(Duration::from_millis(1));
    }

    handle.join().unwrap(); // 阻塞等子线程跑完，再让 main 退出
}
```

运行上面命令看看输出：这次子线程能完整跑完 1~9。

> `join()` 放的位置决定行为：放在主线程循环之后，两者真正并行；放在循环之前，就会先把子线程跑完好再往下走（变成串行）。

## move 闭包：把数据搬进线程

子线程经常要用主线程里的变量。但问题来了：编译器不知道主线程会不会在子线程还在用这个变量时就把它回收了（悬垂引用）。解决办法是在闭包前加 `move`，强制把变量所有权**移动**进子线程：

```rust
use std::thread;

fn main() {
    let v = vec![1, 2, 3];

    // ❌ 不加 move：闭包只借用 v，编译器担心 v 比线程活得短，直接报错
    // let handle = thread::spawn(|| println!("{:?}", v));

    // ✅ move 把 v 的所有权搬进子线程
    let handle = thread::spawn(move || {
        println!("子线程拿到: {:?}", v);
    });

    handle.join().unwrap();
    // println!("{:?}", v); // ❌ v 已经被 move 走，这里不能再用
}
```

`move` 闭包我们在 Day31 见过，这里是它最经典的用途：**跨线程把数据所有权交出去**。

## 批量创建多个线程

实际中常要开 N 个线程。把每个句柄存进 Vec，最后统一 join：

```rust
use std::thread;

fn main() {
    let mut handles = vec![];

    for i in 0..5 {
        // 每个线程 move 进去自己的编号
        let handle = thread::spawn(move || {
            println!("线程 {} 开始干活", i);
        });
        handles.push(handle);
    }

    // 统一等待所有线程结束
    for h in handles {
        h.join().unwrap();
    }
    println!("全部干完");
}
```

## Rust 的无畏并发

其他语言写多线程，最难防的是**数据竞争**：两个线程同时读写同一块内存，结果不可预测。Rust 靠类型系统和所有权，在**编译期**就把这类问题挡下来——你连编译都过不了，根本没机会上线出 bug。

具体靠的是两个标记 trait：`Send`（能安全转移所有权到别的线程）和 `Sync`（引用能安全共享给别的线程）。这两个我们 Day39 再细讲。你现在只要记住：**Rust 多线程出问题，往往在编译期就被拦下了，这是它最香的地方之一。**

## 今日小结

- `thread::spawn(闭包)` 创建子线程，返回 `JoinHandle`
- 主线程一结束程序就退出，子线程会被强杀；用 `handle.join()` 等它跑完
- 子线程要用外部变量时，闭包必须加 `move` 把所有权搬进去
- 批量线程：把 `JoinHandle` 存进 Vec，循环结束后统一 join
- Rust 用类型系统在编译期消除数据竞争，即所谓「无畏并发」

## 动手任务

1. 开两个线程，一个打印 1~5，另一个打印字母 a~e，观察交错输出。
2. 写一个程序：主线程创建 3 个子线程，每个 sleep 1 秒后打印自己的编号，最后主线程打印 done。
3. 故意不加 `move` 就在线程闭包里引用主线程的 Vec，看看编译报错长什么样。

> 想让线程睡一会用 `thread::sleep(Duration::from_millis(100))`，记得 `use std::time::Duration`。

**配合阅读**：[官方书·第16章 使用线程同时运行代码](https://rustwiki.org/zh-CN/book/ch16-01-threads.html)
