# Day43 unsafe Rust

> 目标：搞清楚 `unsafe` 到底放开了什么、没放开什么——它不是「关闭借用检查」，而只是允许少数几类编译器平时禁止的操作，并且你要为安全负责。

## unsafe 是给编译器的承诺

Rust 的借用检查器非常严格，绝大多数时候这是好事。但偶尔：

- 要直接操作硬件、写操作系统内核
- 要和 C 语言库交互（FFI）
- 编译器「太保守」，逻辑上明明安全它却不让过

这时用 `unsafe` 块告诉编译器：「这里的内存安全我自己负责，请放开那几样检查。」

## 最重要的认知：unsafe 不关闭借用检查

新手最大的误会就是以为 `unsafe` 里 Rust 就不管你了。**不是的**：

- `unsafe` 只放开下面**5 类操作**的检查
- 块里的普通引用 `&` / `&mut`，借用规则**照样**检查
- `unsafe` 块要**越小越好**，把危险操作包在里面，外面照样是安全 Rust

## 五类 unsafe 超能力

| 超能力 | 说明 |
|---|---|
| 解引用裸指针 | `*const T` / `*mut T`，可空、可同时有可变+不可变，不保证指向有效内存 |
| 调用 unsafe 函数 | `unsafe fn` 或 C 函数，调用处必须在 unsafe 块里 |
| 访问/修改可变静态变量 | `static mut`，多线程下易数据竞争 |
| 实现 unsafe trait | 比如 `unsafe impl Send for MyType`，自己承诺线程安全 |
| 访问 union 字段 | union 同一时刻只能用一个字段，编译器无法判断当前是哪种 |

## 裸指针：*const / *mut

在安全代码里**就能创建**裸指针，但**解引用必须在 unsafe 块里**：

```rust
fn main() {
    let mut num = 5;

    // 安全代码里就能把引用强转成裸指针
    let r1 = &num as *const i32;  // 不可变裸指针
    let r2 = &mut num as *mut i32; // 可变裸指针

    unsafe {
        println!("r1 = {}, r2 = {}", *r1, *r2); // 解引用必须 unsafe
    }
}
```

裸指针比引用「野」：可以为空、可以同时存在多个可变+不可变、甚至可以指向根本不属于你的内存。所以解引用它是你的责任。

## 调用 unsafe 函数

一个 `unsafe fn` 表示「调用者必须保证某些前提成立」。调用它必须在 `unsafe` 块里：

```rust
unsafe fn dangerous() {} // 不安全函数

fn main() {
    unsafe {
        dangerous(); // 调用处必须包 unsafe
    }
}
```

### 把 unsafe 包成安全抽象

这是 Rust 的最佳实践：**内部用裸指针实现，对外暴露安全函数**。标准库的 `slice::split_at_mut` 就是这么干的——它能把一个 `&mut [T]` 拆成两个 `&mut [T]`，编译器光看类型判断不出这两块不重叠，内部只能靠裸指针，但调用方完全感觉不到 unsafe：

```rust
use std::slice;

fn split_at_mut(slice: &mut [i32], mid: usize) -> (&mut [i32], &mut [i32]) {
    let len = slice.len();
    let ptr = slice.as_mut_ptr();
    assert!(mid <= len); // 我们自己保证 mid 合法

    unsafe {
        (
            slice::from_raw_parts_mut(ptr, mid),
            slice::from_raw_parts_mut(ptr.add(mid), len - mid),
        )
    }
}

fn main() {
    let mut v = vec![1, 2, 3, 4];
    let (a, b) = split_at_mut(&mut v, 2);
    assert_eq!(a, &mut [1, 2]);
    assert_eq!(b, &mut [3, 4]);
}
```

你以后写 no_std 内核（Day63 起）会大量遇到这种「unsafe 在内、安全外壳」的模式。

## FFI：和 C 互操作

`extern "C"` 块用来声明 C 语言函数，调用它天然是 unsafe：

```rust
extern "C" {
    fn abs(input: i32) -> i32;
}

fn main() {
    unsafe {
        println!("abs(-3) = {}", abs(-3)); // 调用 C 标准库
    }
}
```

反过来把 Rust 函数导出给 C 用，要加 `#[no_mangle]` 和 `extern "C"`。这是写操作系统、绑定 C 库的基础。

## static mut

静态变量 `static` 有固定内存地址（和 `const` 在使用处复制不同）。读它是安全的，但**读写 `static mut` 必须 unsafe**，因为多线程同时写就是数据竞争：

```rust
static mut COUNTER: u32 = 0;

unsafe fn add_to_counter() {
    COUNTER += 1; // 读写 static mut 都要 unsafe
}
```

> 日常应用开发尽量别用 `static mut`，用 Day39 的 `Mutex` 更安全。

## 今日小结

- `unsafe` 只放开 5 类操作的检查，**不关闭借用检查**；块越小越好
- 五类：解引用裸指针、调 unsafe 函数、读写 static mut、实现 unsafe trait、访问 union
- 裸指针可在安全代码创建，但解引用必须 unsafe
- 最佳实践：内部 unsafe、对外包成安全抽象（如 `split_at_mut`）
- FFI 用 `extern "C"` 声明 C 函数，调用需 unsafe

## 动手任务

1. 把一个 `&i32` 强转成 `*const i32`，在 unsafe 块里解引用打印。
2. 读一遍 `split_at_mut` 例子，亲手跑通，体会「调用方不用 unsafe」。
3. 查文档：写一个 `extern "C"` 声明，调用标准库的某个 C 函数（比如 `abs`）。

> 你在内核开发（rCore）里会天天和 unsafe 打交道，今天建立的「unsafe 不万能、要包安全外壳」意识很重要。

**配合阅读**：[官方书·第19章 不安全的 Rust](https://rustwiki.org/zh-CN/book/ch19-01-unsafe-rust.html)
