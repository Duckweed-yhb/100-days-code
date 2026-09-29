# Day38 消息传递

> 目标：学会用 `mpsc` channel 在线程之间传消息，理解「通过通信共享内存」这条 Rust 推崇的并发姿势。

## 两种并发姿势

多线程共享数据，传统做法是「共享内存」：大家抢同一块内存，靠锁互斥（Day39 讲）。Rust 社区更推崇另一句口号：

> **不要通过共享内存来通信，而要通过通信来共享内存。**

意思是：别让多个线程盯着同一块内存抢，而是开一条「管道」，一个线程把数据塞进管道，另一个从另一端取出来。数据跟着消息走，同一时刻只有一个线程拥有它，天然没有数据竞争。

这条管道就是标准库的 channel（通道）。

## mpsc：多生产者、单消费者

`std::sync::mpsc` = **m**ultiple **p**roducer, **s**ingle **c**onsumer（多个发送端、一个接收端）。调用 `mpsc::channel()` 会返回一对 `(tx, rx)`：

- `tx`（transmitter 发送端）：往管道里塞东西
- `rx`（receiver 接收端）：从管道另一头取东西

```rust
use std::thread;
use std::sync::mpsc;

fn main() {
    let (tx, rx) = mpsc::channel(); // 建一条管道

    // 把 tx move 进子线程
    thread::spawn(move || {
        let val = String::from("hi");
        tx.send(val).unwrap(); // 发送；send 会拿走 val 的所有权
        // println!("{}", val); // ❌ val 已经被移动走，不能再用
    });

    // 主线程从 rx 接收
    let received = rx.recv().unwrap(); // 阻塞，直到收到一条消息
    println!("收到: {}", received); // 收到: hi
}
```

两个重点：

1. **`send()` 会拿走数据的所有权**。发送后原变量就不能再用了——这正好保证「消息发出去，原线程就别碰了」。
2. **`recv()` 会阻塞**，直到真的收到一条消息才返回。返回 `Result`，管道关闭时是 `Err`。

## 发多条消息，把 rx 当迭代器

接收端可以直接 `for received in rx` 当迭代器用：每收到一条就迭代一次，发送端全部 drop（管道关闭）时迭代自动结束。

```rust
use std::thread;
use std::sync::mpsc;
use std::time::Duration;

fn main() {
    let (tx, rx) = mpsc::channel();

    thread::spawn(move || {
        for word in vec!["hi", "from", "thread"] {
            tx.send(word).unwrap();
            thread::sleep(Duration::from_secs(1)); // 每条间隔 1 秒
        }
    });

    // rx 当迭代器：有消息就打印，发送端关闭后自动结束
    for received in rx {
        println!("收到: {}", received);
    }
}
```

运行上面命令看看输出：每秒收到一个词，发完后程序自然结束。

> 如果不想阻塞等待，用 `try_recv()`：立刻返回 `Result`，没消息就 `Err`，适合轮询场景。

## 多个发送者：clone 发送端

`mpsc` 的 `m` 就是「多生产者」。怎么得到第二个发送端？`tx.clone()` 即可：

```rust
use std::thread;
use std::sync::mpsc;

fn main() {
    let (tx, rx) = mpsc::channel();
    let tx2 = tx.clone(); // 克隆一个发送端

    // 第一个线程用 tx
    thread::spawn(move || {
        tx.send("来自线程一").unwrap();
    });

    // 第二个线程用 tx2
    thread::spawn(move || {
        tx2.send("来自线程二").unwrap();
    });

    for received in rx {
        println!("{}", received);
    }
}
```

两个线程往同一条管道塞消息，主线程统一接收。这就是「多生产者」的用法。

## 一个完整小例子：子线程计算结果发回来

```rust
use std::thread;
use std::sync::mpsc;

fn main() {
    let (tx, rx) = mpsc::channel();

    for i in 0..4 {
        let tx = tx.clone(); // 每个线程克隆一份发送端
        thread::spawn(move || {
            let result = i * i; // 模拟一点计算
            tx.send(format!("线程 {} 算出 {}", i, result)).unwrap();
        });
    }

    // 最初的 tx 留在主线程，drop 它，否则 rx 永远不关闭
    drop(tx);

    for msg in rx {
        println!("{}", msg);
    }
}
```

> 注意最后那个 `drop(tx)`：只要还有任何一个发送端活着，`rx` 就不会关闭，`for received in rx` 就不会结束。主线程克隆出来的那份不用了，记得 drop 掉。

## 今日小结

- channel 是线程间通信的管道：`mpsc::channel()` 返回 `(tx, rx)`
- `tx.send(值)` 发送并拿走所有权；`rx.recv()` 阻塞接收，`try_recv()` 不阻塞
- `rx` 可直接当迭代器，发送端全部 drop 后迭代结束
- `tx.clone()` 得到多个发送端，实现多生产者
- 「通过通信共享内存」比共享内存更安全：数据跟着消息走，无数据竞争

## 动手任务

1. 建一个 channel，子线程发送 5 个数（每秒一个），主线程用 `for received in rx` 全部打印。
2. 开 3 个生产者线程，每个发送自己的名字，主线程收集并打印所有消息。
3. 查文档了解 `try_recv`，写一个不阻塞、每隔一段时间轮询一次 rx 的小循环。

> 程序卡住不退出？检查是不是忘了 `drop(tx)`，导致接收端一直等消息。

**配合阅读**：[官方书·第16章 消息传递并发](https://rustwiki.org/zh-CN/book/ch16-02-message-passing.html)
