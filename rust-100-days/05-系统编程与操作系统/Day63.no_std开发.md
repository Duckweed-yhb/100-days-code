# Day63 no_std 开发

> 目标：学会写一个不依赖标准库（`std`）的 Rust 程序，搞清楚 `core`/`alloc` 分别是什么、为什么没有分配器就用不了 `Vec`。

普通 Rust 程序一上来 `fn main`，能 `println!`、能开文件、能建线程——这些都靠**标准库 `std`**。可标准库默认会假设你跑在操作系统上：它要堆分配器、要系统调用、要文件系统。内核这些都没有，所以第一件事就是**把 `std` 扔掉**。

## #![no_std]：跟标准库说拜拜

在 crate 根文件最顶部加一行：

```rust
// 内核代码片段，需在 rCore 环境运行，不能在普通 cargo 项目直接跑
#![no_std]

fn main() {
    println!("hello"); // 报错！println! 在 std 里
}
```

加了 `#![no_std]` 之后，`std` 整个不可用，你手里只剩一个**核心库 `core`**。

## core vs std：剥掉系统相关的部分

| 库 | 内容 | 依赖 |
|---|---|---|
| `core` | 最基础的类型、`trait`、迭代器、原子类型、格式化（`write!`）等 | 不依赖操作系统，裸机可用 |
| `std` | 在 `core` 之上加了：堆分配 `Vec/String`、文件、线程、时间、环境变量、IO | 依赖操作系统与分配器 |

`core` 里绝大多数东西你都认识：`Option`、`Result`、`match`、闭包、迭代器，全在。变化主要是：

- 没有 `std::println!`，但有 `core::write!` / `core::format_args!`（你得自己提供"输出到哪"）。
- 没有 `std::Thread`、没有 `std::fs`、没有 `Instant`。
- `Vec`/`String` 默认**也没了**——它们属于 `alloc` crate，不是 `core`。

## 为什么 Vec/String 用不了

`Vec` 需要在堆上动态申请内存，而"堆"是**分配器（allocator）**给的。`std` 默认给你链接一个系统分配器（调用操作系统 `malloc`）。`no_std` 环境里没有操作系统，自然没有默认分配器。

想用 `Vec`/`String`/`Box`，需要两步：

```rust
#![no_std]
#![feature(alloc_error_handler)] // nightly，声明分配错误处理

extern crate alloc; // 引入 alloc crate，它不依赖 OS，但要你提供分配器

use alloc::vec::Vec;
use alloc::string::String;

#[alloc_error_handler]
fn alloc_error(_: core::alloc::Layout) -> ! {
    panic!("out of memory");
}
```

然后你还得用 `#[global_allocator]` 挂一个自己实现的堆分配器（rCore 里就是 Day72 讲的物理页分配器再包一层）。在那之前，老老实实只用**栈上固定大小**的数据：数组、`&[u8]`、裸指针。

## #[panic_handler]：panic 了怎么办

`std` 里 panic 会打印错误信息再 abort。`no_std` 没人管 panic，编译器要求你自己定义一个 **panic 处理函数**：

```rust
// 内核代码片段，需在 rCore 环境运行，不能在普通 cargo 项目直接跑
#![no_std]
// 某些 nightly 版本需要 `#![feature(panic_handler)]`，稳定版直接用属性即可

#[panic_handler]
fn panic(_info: &core::panic::PanicInfo) -> ! {
    // 内核里：点亮错误 LED / 往串口打一行 / 死循环
    loop {}
}
```

要点：

- 函数返回类型必须是 `!`（永不返回），因为 panic 后程序不该继续。
- 函数体里你啥都不能保证能做，最安全就是 `loop {}` 卡住，或者往你已经初始化好的串口写点东西。

## 一个最小 no_std 库长什么样

```rust
// 内核代码片段，需在 rCore 环境运行，不能在普通 cargo 项目直接跑
#![no_std]

#[panic_handler]
fn panic(_info: &core::panic::PanicInfo) -> ! {
    loop {}
}

// 一个纯计算函数，不碰 IO，no_std 下随便写
fn add(a: usize, b: usize) -> usize {
    a + b
}
```

注意：这是**库 crate（lib）**的样子。如果是要生成能跑的**可执行文件（binary）**，还得加 `#![no_main]`、自己定入口——那是 Day64 的事。

## 今日小结

- `#![no_std]` 剥掉标准库，只留 `core`，适合内核/嵌入式这种没有 OS 的环境
- `core` = 语言本身，`std` = core + 系统能力（堆/文件/线程）
- `Vec`/`String` 在 `alloc` crate 里，要用必须自己挂全局分配器
- `#[panic_handler]` 是 `no_std` 程序的硬性要求，panic 后必须永不返回

## 动手任务

1. 新建一个普通 cargo 项目，加 `#![no_std]` 和一个空的 `#[panic_handler]`，尝试 `cargo build`，观察报错。
2. 故意在 no_std 里写 `let v: Vec<i32> = Vec::new();`，看看编译器怎么提示你需要 `alloc`。
3. 读 rCore `os/main.rs` 顶部，找出 `#![no_std]`、`extern crate alloc`、`#[panic_handler]` 分别在哪。

**配合阅读**：[rCore-Tutorial·第一章 应用程序与基本执行环境（移除标准库依赖）](https://rcore-os.cn/rCore-Tutorial-Book-v3/chapter1/index.html)
