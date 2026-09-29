# Day40 异步编程

> 目标：搞懂 `async`/`await` 到底做了什么——它和多线程有何不同、`Future` 为什么是状态机、为什么需要运行时，并写出第一个能跑的 tokio 异步程序。

## 阻塞 vs 让出

先回顾多线程：上三天我们开一个系统线程，让它在 `recv()`、`lock()` 那里**阻塞**等着——操作系统把这个线程挂起，切到别的线程跑。一个阻塞的操作就要占住一个系统线程，线程是有成本的。

异步编程换了个思路：一个线程里同时挂着成千上万个任务，某个任务要等 IO（网络、磁盘）时，它**不阻塞线程**，而是说「我先让开，你们干别的，等数据到了再叫我」。这样一个线程就能同时伺候海量任务，特别适合网络服务这种「大量时间在等」的场景。

关键区分：

- 多线程：并行靠**操作系统**切换线程，阻塞也占一个线程
- 异步：并发靠**程序自己**在单线程内切换任务，等待时不占线程

## async fn 返回的是 Future

你大概见过这样的写法：

```rust
async fn say_hello() -> String {
    "hello".to_string()
}

fn main() {
    let task = say_hello(); // 注意：这一行什么都没打印！
}
```

`say_hello` 前面加了 `async`，调用它**不会立刻执行函数体**，而是返回一个实现了 `Future` trait 的值——你可以把它理解成一个「还没开始跑的任务」/「未来的值」。真正的函数体一行都没执行。

这和 Day32 迭代器的惰性很像：创建迭代器不干活，迭代器不执行。

## .await：把 Future 跑完

要真正执行它，得用 `.await`：

```rust
async fn say_hello() -> String {
    "hello".to_string()
}

async fn main() {
    let s = say_hello().await; // 这里才真正执行，拿到 String
    println!("{}", s);
}
```

`.await` 做的事：如果这个 Future 已经完成，直接拿结果；如果还没完成（比如在等网络），就**让出当前任务的控制权**，去跑别的任务，等这个 Future 就绪了再回来继续。

> 重点：`.await` **不阻塞线程**，只让出当前异步任务。这和 `std::thread::sleep`、`rx.recv()` 那种真阻塞完全不同。

## Future 是怎么跑起来的：需要运行时

上面代码里 `async fn main()` 其实不能直接 `cargo run`——Rust 标准库**没有内置异步运行时**。`Future` 只是一个接口，得有人来「驱动」它：负责轮询各个任务、在它们就绪时唤醒、把它们调度到线程上。这个驱动器叫**异步运行时（runtime）**，最流行的是 [tokio](https://crates.io/crates/tokio)。

## 写第一个 tokio 程序

在你的 Rust 项目里改 `Cargo.toml`，加上 tokio 依赖：

```toml
[package]
name = "async-demo"
version = "0.1.0"
edition = "2021"

[dependencies]
tokio = { version = "1", features = ["full"] }
```

> 首次 `cargo run` 会联网拉取 tokio 及其依赖，请耐心等一下。

然后写 `src/main.rs`：

```rust
use std::time::Duration;

// tokio 提供的 #[tokio::main] 宏，帮你启动异步运行时
#[tokio::main]
async fn main() {
    // 同时派两个异步任务
    let t1 = tokio::spawn(async {
        task_one().await;
    });
    let t2 = tokio::spawn(async {
        task_two().await;
    });

    // 等两个都完成
    let _ = tokio::join!(t1, t2);
}

async fn task_one() {
    for i in 1..=3 {
        println!("任务一: 第 {} 步", i);
        // 异步睡眠：让出控制权，不阻塞线程
        tokio::time::sleep(Duration::from_secs(1)).await;
    }
}

async fn task_two() {
    for i in 1..=3 {
        println!("任务二: 第 {} 步", i);
        tokio::time::sleep(Duration::from_secs(1)).await;
    }
}
```

运行下面命令看看输出：

```powershell
cargo run
```

你会看到「任务一」「任务二」的打印交错出现，两个任务在并发推进——但它们可能跑在同一个 OS 线程上。

## 心智模型

| | 多线程（Day37~39） | 异步 async/await |
|---|---|---|
| 调度者 | 操作系统 | 异步运行时（tokio） |
| 等待时 | 阻塞占住一个线程 | 让出，线程去跑别的任务 |
| 成本 | 线程较重，几千个就吃力 | 任务很轻，可同时跑成千上万个 |
| 典型场景 | CPU 密集并行 | IO 密集（网络、数据库） |

经验法则：CPU 密集计算用多线程；大量等网络/IO 的服务用异步。真实项目里两者常常混用。

## 今天先到这

异步是个大坑，运行时、`Pin`、`Waker`、自执行 Future 这些底层概念现在不用深究。你今天只要建立三个正确认知：

1. `async fn` 返回 `Future`，调用它不执行，`.await` 才驱动它
2. `.await` 让出任务，不阻塞线程
3. 必须有运行时（tokio）才能跑起来

## 今日小结

- 异步用「任务让出」代替「线程阻塞」，一个线程可伺候海量 IO 任务
- `async fn` 返回 `impl Future`，调用时不执行，是惰性的
- `.await` 驱动 Future 执行，未就绪时让出控制权而非阻塞线程
- Rust 标准库不提供运行时，要靠 tokio；`#[tokio::main]` 启动它
- 多线程适合 CPU 密集，异步适合 IO 密集

## 动手任务

1. 新建一个 cargo 项目，按上面 `Cargo.toml` 加 tokio，把示例跑通，观察两个任务交错打印。
2. 把 `tokio::time::sleep` 换成标准库的 `std::thread::sleep`，观察输出变化（它会真阻塞，两个任务变成串行）。
3. 再加一个 `task_three`，用 `tokio::spawn` 同时跑三个任务。

> `cargo run` 卡在下载依赖？那是在拉 tokio，正常现象，别 Ctrl+C。

**配合阅读**：[Rust 语言圣经·异步编程简介](https://course.rs/advance/async/intro.html)
