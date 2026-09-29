# Day45 阶段项目

> 目标：把最近 15 天学的闭包、迭代器、智能指针、多线程、channel 揉到一起，做一个真正能跑的多线程词频统计小程序，并知道接下来可以往哪扩展。

## 项目目标

统计一段英文文本里每个单词出现的次数。单线程写很简单，但我们要**多线程**：把文本切成几块，每个线程独立数自己那块，最后把结果合并。这正好练到：

- `thread::spawn` + `move` 闭包（Day37）
- `mpsc` 通道把每个线程的结果发回来（Day38）
- 迭代器 + 闭包处理每一块文本（Day31/32）
- `HashMap` 汇总结果（Day24）

## 为什么用 channel 合并结果

我们有两种合并方式：

- 方式 A：`Arc<Mutex<HashMap>>`，所有线程往同一个 map 里抢着写
- 方式 B：每个线程数出**自己那块**的小 HashMap，用 channel 发回主线程，主线程统一合并

方式 B 更优雅——线程之间不共享可变状态，各自数各自的，最后在主线程一次性合并，没有锁竞争。这也是「通过通信共享内存」的实践。

## 完整代码

新建一个 cargo 项目：

```powershell
cargo new wordcount
cd wordcount
```

把下面内容整个贴进 `src/main.rs`：

```rust
use std::collections::HashMap;
use std::sync::mpsc;
use std::thread;

/// 统计一段文本里每个单词出现的次数，返回一个 HashMap
fn count_words(text: &str) -> HashMap<String, usize> {
    let mut map = HashMap::new();
    text.split_whitespace() // 按空白切词，迭代器
        .map(|word| word.to_lowercase()) // 统一小写，避免 The/the 分开算
        .map(|word| word.trim_matches(|c: char| !c.is_alphanumeric())) // 去掉首尾标点
        .filter(|word| !word.is_empty()) // 去掉空串
        .for_each(|word| {
            *map.entry(word).or_insert(0) += 1; // 计数 +1
        });
    map
}

fn main() {
    // 示例文本：实际项目里这里换成读文件（Day48 学）
    let text = "Rust is fast. Rust is safe.
                Fast code and safe code, that is Rust.
                Ownership makes Rust safe without a garbage collector.";

    // 把文本按行分成 4 块（这里是演示，实际按字节/行切块）
    let chunks: Vec<&str> = text.lines().collect();
    let n_workers = chunks.len().max(1);

    // 建通道：每个 worker 把它的 HashMap 发回来
    let (tx, rx) = mpsc::channel();

    println!("启动 {} 个统计线程...\n", n_workers);

    for chunk in chunks {
        let tx = tx.clone(); // 每个线程克隆一份发送端
        // move 把这一块文本和发送端所有权搬进线程
        thread::spawn(move || {
            let local = count_words(chunk); // 线程内部独立统计
            tx.send(local).unwrap(); // 发回主线程
        });
    }

    // 最初的 tx 留在主线程，drop 掉，否则 rx 永远不关闭
    drop(tx);

    // 主线程收集所有小 HashMap，合并成一个大的
    let mut total: HashMap<String, usize> = HashMap::new();
    for local in rx {
        for (word, count) in local {
            *total.entry(word).or_insert(0) += count;
        }
    }

    // 按出现次数从高到低排序打印
    let mut pairs: Vec<_> = total.into_iter().collect();
    pairs.sort_by(|a, b| b.1.cmp(&a.1)); // 次数降序

    println!("词频统计结果：");
    for (word, count) in pairs {
        println!("  {:<12} 出现 {} 次", word, count);
    }
}
```

运行：

```powershell
cargo run
```

你会看到类似输出（`rust`、`safe`、`code` 排在前面）。

## 这段代码用到了哪些天的知识

- `text.lines()`、`split_whitespace()`、`.map().filter().for_each()` → Day32 迭代器
- `.map(|word| word.to_lowercase())` 里竖线后面 → Day31 闭包
- `thread::spawn(move || ...)` → Day37 线程 + move
- `mpsc::channel()`、`tx.send`、`for local in rx`、`drop(tx)` → Day38 消息传递
- `HashMap::new()`、`entry().or_insert()` → Day24

## 可以怎么扩展

挑一两个继续练手：

1. **真读文件**：用 `std::fs::read_to_string` 读一个 `.txt` 文件（Day48 会系统学文件 IO），改成统计真实英文书。
2. **按字节等分切块**：现在是按行切，改成把文本按字节平均分成 N 段，更接近真实并行分块。
3. **方式 A 对比**：用 `Arc<Mutex<HashMap>>` 重写一版，对比两版代码，体会「共享状态」和「消息传递」两种风格。
4. **Top K**：只打印出现次数最多的前 5 个词（提示：排序后取前几个，或用 `BinaryHeap`）。
5. **过滤停用词**：准备一个 `["the", "a", "is", ...]` 的集合，统计时跳过它们。

## 阶段复盘

到这里，你已经走完 Rust 进阶特性这一阶段：

- 闭包 + 迭代器让你写出函数式、零成本的处理流水线
- Box / Rc / RefCell / Weak / Arc 让你在所有权规则内灵活管理堆数据与共享
- 多线程 + channel + Mutex 让你写出编译期就安全的并发代码
- 异步、trait 对象、unsafe、宏则是你以后写库、写内核的进阶工具箱

下一阶段（Day46 起）我们进入工程化与网络项目，把这些零件组装成真正的命令行工具和 Web 服务器。

## 今日小结

- 综合项目：多线程词频统计，演示了如何把学过的零件组装起来
- 用 channel 把每个线程的局部结果发回主线程合并，避免共享可变状态
- `count_words` 完全用迭代器 + 闭包写成，零手写循环
- 别忘了 `drop(tx)`，否则接收端永远等不到关闭
- 下一阶段进入工程化与网络项目

## 动手任务

1. 把上面代码跑通，确认输出和你的手算一致。
2. 加一个停用词过滤器，把 `is`、`the`、`a` 这类词排除掉。
3. 用 `Arc<Mutex<HashMap>>` 重写合并逻辑，对比两种写法的手感。

> 结果对不上？先确认 `to_lowercase()` 和去标点有没有把同一个词拆成两个。

**配合阅读**：[官方书·第13章 用迭代器改进 I/O 项目](https://rustwiki.org/zh-CN/book/ch13-03-improving-our-io-project.html)
