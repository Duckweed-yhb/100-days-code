# Day25 错误处理

> 目标：分清「不可恢复的 bug」和「可恢复的错误」，学会用 `panic!`、`Result` 和 `?` 运算符优雅地处理错误。

## Rust 把错误分成两类

别的语言通常用一套异常机制包打天下。Rust 把错误明确分成两类：

| 错误类型 | 什么时候发生 | 怎么处理 |
|---|---|---|
| **不可恢复错误** | 代码出 bug、遇到了不该发生的情况 | `panic!` 直接崩溃退出 |
| **可恢复错误** | 文件不存在、用户输入非法这种预期内的事 | `Result<T, E>` 返回给调用者处理 |

这个区分很关键：不是所有错误都该崩溃。打开一个不存在的文件是再正常不过的事，程序应该优雅地告诉用户「文件没找到」，而不是直接闪退。

## panic!：不可恢复错误

`panic!` 宏一旦执行，程序会打印错误信息、展开清理栈，然后退出：

```rust
fn main() {
    panic!("程序炸了");
}
```

你平时其实已经在间接触发它了：数组越界访问 `v[99]`、对 `None` 调 `unwrap()`、整数除以 0，底层都是 panic。

### 用 RUST_BACKTRACE 看调用栈

panic 会告诉你出错位置，但有时它发生在标准库内部（比如切片越界），需要看完整调用栈才能找到自己代码里的源头。PowerShell 里这样跑：

```powershell
$env:RUST_BACKTRACE=1; cargo run
```

读 backtrace 的诀窍：**从上往下找第一个属于你自己文件名的行**（如 `src/main.rs:4`），那就是问题发源地。要用 debug 模式（别加 `--release`）才有完整 backtrace。

## Result<T, E>：可恢复错误

`Result` 是标准库枚举，和 `Option` 一样被自动引入作用域：

```rust
enum Result<T, E> {
    Ok(T),  // 成功，里面是结果值 T
    Err(E), // 失败，里面是错误信息 E
}
```

打开文件就是典型例子：

```rust
use std::fs::File;

fn main() {
    let f = File::open("hello.txt"); // 返回 Result<File, io::Error>
}
```

文件可能存在（`Ok(file)`），也可能不存在（`Err(error)`）。你必须处理这两种情况。

### 用 match 处理

```rust
use std::fs::File;
use std::io::ErrorKind;

fn main() {
    let f = File::open("hello.txt");

    let f = match f {
        Ok(file) => file, // 成功，拿到文件句柄
        Err(error) => match error.kind() {
            // 文件不存在就新建一个
            ErrorKind::NotFound => match File::create("hello.txt") {
                Ok(fc) => fc,
                Err(e) => panic!("创建文件失败: {:?}", e),
            },
            // 其他错误直接崩溃
            other_error => panic!("打开文件失败: {:?}", other_error),
        },
    };
}
```

能用，但嵌套 match 有点丑。下面两个简写能救你。

## unwrap 和 expect：失败就 panic

- **`unwrap()`**：是 `Ok` 就取出值；是 `Err` 就自动 panic（用默认错误信息）。
- **`expect("自定义消息")`**：一样，但 panic 时打印你指定的消息，更容易定位是哪行出的问题。

```rust
use std::fs::File;

fn main() {
    // 出错时用默认信息 panic
    let f = File::open("hello.txt").unwrap();

    // 出错时打印 "Failed to open hello.txt: ..."
    let f = File::open("hello.txt").expect("Failed to open hello.txt");
}
```

经验法则：写原型、示例、测试时用 `unwrap`/`expect` 很爽；正式代码里别滥用，要把错误体面地传回给调用者。

## ? 运算符：错误自动传播

写一个函数，里面连续调用好几个可能失败的操作，每个都手写 match 会烦死人。`?` 运算符就是干这个的：

```rust
use std::fs::File;
use std::io;
use std::io::Read;

// 返回 Result<String, io::Error>：成功给字符串，失败把错误往上抛
fn read_username_from_file() -> Result<String, io::Error> {
    // 如果是 Ok，把里面的 File 赋给 f；
    // 如果是 Err，立即 return Err(...) 给调用者
    let mut f = File::open("hello.txt")?;
    let mut s = String::new();
    f.read_to_string(&mut s)?; // 同理
    Ok(s) // 全部成功，包成 Ok 返回
}
```

`?` 的工作原理一句话：**成功就解包继续，失败就提前 return。** 它还会自动用 `From` trait 把错误类型转成函数返回类型要求的那个，所以 `open` 和 `read_to_string` 返回的不同错误类型能统一成 `io::Error`。

`?` 还能链式写得更短：

```rust
fn read_username_from_file() -> Result<String, io::Error> {
    let mut s = String::new();
    File::open("hello.txt")?.read_to_string(&mut s)?;
    Ok(s)
}

// 标准库甚至提供了一步到位的函数
fn read_username() -> Result<String, io::Error> {
    std::fs::read_to_string("hello.txt")
}
```

### ? 只能用在返回 Result 的函数里

`main` 默认返回 `()`，不能直接加 `?`。想在 main 里用，改一下签名：

```rust
use std::error::Error;
use std::fs::File;

fn main() -> Result<(), Box<dyn Error>> {
    let f = File::open("hello.txt")?; // 现在可以了
    Ok(())
}
```

`Box<dyn Error>` 表示「任何类型的错误」，是 main 里的常见写法（`dyn` 我们后面才讲，先照抄）。

## 何时 panic，何时 Result

| 场景 | 选择 |
|---|---|
| 示例、原型、测试 | `unwrap` / `expect`，图省事 |
| 违反编程契约（传了不可能的值） | `panic!`，这是调用方的 bug |
| 错误是预期内的（用户输入、文件不存在） | 返回 `Result` |
| 函数可能因多种原因失败 | `Result` + `?`，交给上层决定 |

核心原则：**返回 `Result` 是默认选择；`panic!` 留给「不该发生」的 bug。**

## 今日小结

- 不可恢复错误用 `panic!` 崩溃；可恢复错误用 `Result<T, E>` 返回
- `RUST_BACKTRACE=1 cargo run` 能看调用栈，找自己代码里出错的行
- `unwrap()`/`expect("...")` 是「失败就 panic」的简写，适合原型
- `?` 运算符：Ok 解包继续，Err 提前 return，自动转换错误类型
- `?` 只能用在返回 `Result`/`Option` 的函数里；main 要用得改签名 `-> Result<(), Box<dyn Error>>`

## 动手任务

1. 写一个函数 `fn read_note() -> Result<String, std::io::Error>`，用 `std::fs::read_to_string` 读一个存在的文件，成功返回内容。
2. 故意读一个不存在的文件名，在 `main`（改成返回 `Result`）里用 `?` 传播，观察程序打印的错误信息。
3. 对 `File::open("x.txt")` 分别用 `unwrap()` 和 `expect("打开 x.txt 失败")`，对比两种 panic 信息的区别。

> 提示：PowerShell 下设置环境变量是 `$env:RUST_BACKTRACE=1; cargo run`。

**配合阅读**：[官方书·第9章 panic! 与不可恢复的错误](https://rustwiki.org/zh-CN/book/ch09-01-unrecoverable-errors-with-panic.html)
