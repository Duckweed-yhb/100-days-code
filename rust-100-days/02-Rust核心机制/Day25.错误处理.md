# Day25 错误处理

> **前置知识**：Day18 Option 与空值（`Result` 和它是一对）、Day19 match 模式匹配（取出 `Result` 里的值）、Day20 if-let 与 while-let
> **难度**：进阶
> **预计阅读时间**：25 分钟
> **代码说明**：本章代码都放在**同一个** `main.rs` 里。每块都带完整的 `fn main()`（或者能直接用的完整函数），单独复制就能跑。
> 每块前面标了 `📦`（能单独跑）或 `⚠️`（给你看的源码，别抄）。有几块是故意会 panic 的，那正是本节要观察的行为。

---

## Rust 把错误分成两类

别的语言一般用一套异常机制包住所有错误，Rust 分得细一些：

| 错误类型 | 什么时候发生 | 怎么处理 |
|---|---|---|
| **不可恢复错误** | 代码出了 bug，碰上不该发生的情况 | `panic!`，程序直接崩 |
| **可恢复错误** | 文件不存在、用户输入不合法，这类预期内的事 | 用 `Result<T, E>` 返回给调用者 |

这个区分有用：打开一个不存在的文件太常见了，程序该告诉用户「文件没找到」，而不是闪退。

## panic! 与不可恢复错误

`panic!` 是个宏，一执行就打印错误信息、展开并清理调用栈，然后退出。

📦 本块能单独跑（一跑就崩，崩了才算跑对）：

```rust
fn main() {
    panic!("程序炸了");
}
```

你平时已经间接用过它了：数组越界访问 `v[99]`、对 `None` 调 `unwrap()`、整数除以 0，底层走的都是 panic。

### 用 RUST_BACKTRACE 看调用栈

panic 会告诉你出错的位置，但如果错发生在标准库内部（比如切片越界），光看那一行不清楚是哪句代码引起的。把 backtrace 打开：

```powershell
$env:RUST_BACKTRACE=1; cargo run
```

读 backtrace 的诀窍：**从上往下找第一个带你自己文件名的那行**（比如 `src/main.rs:4`），那就是问题发源地。要用 debug 模式跑（别加 `--release`），不然符号信息不全。

## Result<T, E> 与可恢复错误

`Result` 是标准库里的枚举，和 `Option` 一样会被自动带进作用域。

⚠️ 本块是标准库源码，给你看的，不要抄进自己的文件：

```rust
pub enum Result<T, E> {
    Ok(T),  // 成功，里面装着结果值 T
    Err(E), // 失败，里面装着错误信息 E
}
```

Day17 的踩坑小节里记过这件事：照着标准库写一遍 `enum Option<T>`，自己定义的 `Option` 和 std 的会变成两个类型，然后报出一串看不懂的错。同名的东西别自己再定义一遍。

打开文件是典型例子：

📦 本块能单独跑：

```rust
use std::fs::File;

fn main() {
    let f = File::open("hello.txt"); // 返回 Result<File, io::Error>
    // 文件在就打印 Ok(...)，不在就打印 Err(...)，两种情况都不会崩
    println!("{:?}", f);
}
```

文件可能在（`Ok(file)`），也可能不在（`Err(error)`），这两种你都得管。

### 用 match 处理

📦 本块能单独跑：

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
            // 其他错误直接崩
            other_error => panic!("打开文件失败: {:?}", other_error),
        },
    };

    println!("拿到文件了: {:?}", f);
}
```

`error.kind()` 返回一个 `ErrorKind` 枚举，用来分辨具体是哪种错误。这段嵌套了两层 match、里面还套了一层，能用，但确实不好看。

## unwrap 和 expect，失败就崩

- **`unwrap()`**：`Ok` 就把值取出来，`Err` 就自动 panic（用默认的错误信息）。
- **`expect("自定义消息")`**：一样会 panic，但会带上你写的消息，方便定位是哪行出的问题。

📦 本块能单独跑（`hello.txt` 不存在的话第一行就崩）：

```rust
use std::fs::File;

fn main() {
    // 出错时用默认信息 panic
    let f = File::open("hello.txt").unwrap();

    // 出错时 panic 信息里会带上 "Failed to open hello.txt"
    let f2 = File::open("hello.txt").expect("Failed to open hello.txt");

    println!("{:?} {:?}", f, f2);
}
```

这两行是分开演示的：`unwrap` 那行先崩了，`expect` 那行就执行不到了。想看到 `expect` 打印的信息，把第一行删掉再跑。

原型、示例、测试里用 `unwrap` / `expect` 很省事；正式代码里别滥用，要把错误交回给调用者。

## 用 ? 把错误往上传

一个函数里连着调好几个可能失败的操作，每个都手写 match 会很啰嗦。`?` 就是为这个准备的：

📦 本块能单独跑：

```rust
use std::fs;
use std::fs::File;
use std::io;
use std::io::Read; // read_to_string 挂在 Read 这个 trait 上，得先引进来

// 成功给字符串，失败把错误往上抛
fn read_username_from_file() -> Result<String, io::Error> {
    // 是 Ok，就把里面的 File 给 f；是 Err，立刻 return Err(...) 给调用者
    let mut f = File::open("hello.txt")?;
    let mut s = String::new();
    f.read_to_string(&mut s)?; // 同理
    Ok(s) // 全部成功，包成 Ok 返回
}

fn main() {
    // 先写一个 hello.txt 出来，保证下面读得到。
    // 把这一行注释掉，程序就会走 Err 分支，打印 "读失败: ..."
    fs::write("hello.txt", "yhb").unwrap();

    match read_username_from_file() {
        Ok(name) => println!("读到用户名: {}", name),
        Err(e) => println!("读失败: {}", e),
    }
}
```

`?` 干的事一句话说完：**成功就解包继续往下走，失败就提前 return。** 它还会用 `From` trait 把错误转成函数返回类型要求的那个类型，所以 `File::open` 和 `read_to_string` 报出来的不同错误类型能统一成 `io::Error`。

`use std::io::Read;` 那行的原因：`read_to_string` 不是 `File` 自带的方法，它是 `Read` trait 提供的方法，trait 不在作用域里就调不出来。trait 本身 Day27 会讲，这里先记住「报错说找不到方法时，先看看是不是少 use 了 trait」。

`?` 还能链式写得更短：

📦 本块能单独跑：

```rust
use std::fs;
use std::fs::File;
use std::io;
use std::io::Read;

fn read_username_from_file() -> Result<String, io::Error> {
    let mut s = String::new();
    File::open("hello.txt")?.read_to_string(&mut s)?;
    Ok(s)
}

fn main() {
    fs::write("hello.txt", "yhb").unwrap();
    println!("{:?}", read_username_from_file());
}
```

标准库甚至给了一步到位的函数，连 `?` 都不用写：

📦 本块能单独跑：

```rust
use std::fs;
use std::io;

// 打开、读完、关掉，出错仍然返回 Result 交给你
fn read_username() -> Result<String, io::Error> {
    std::fs::read_to_string("hello.txt")
}

fn main() {
    fs::write("hello.txt", "yhb").unwrap(); // 先造出文件，跟上面两块一样
    match read_username() {
        Ok(s) => println!("读到: {}", s),
        Err(e) => println!("读失败: {}", e),
    }
}
```

### ? 只能用在返回 Result 的函数里

`main` 默认返回 `()`，直接在里面写 `?` 会被编译器拦下来。想让 `main` 用上 `?`，得改签名：

📦 本块能单独跑：

```rust
use std::error::Error;
use std::fs;

fn main() -> Result<(), Box<dyn Error>> {
    fs::write("hello.txt", "yhb").unwrap(); // 先造出文件，后面才读得到
    let s = fs::read_to_string("hello.txt")?; // 这里可以写 ? 了
    println!("{}", s);
    Ok(()) // 成功时手动返回 Ok
}
```

`Box<dyn Error>` 的意思是「任何类型的错误」，`main` 里常见这么写。`dyn` 后面才讲，先照抄。

结尾那个 `Ok(())` 里的 `()` 是空元组，表示「没有值」，函数成功时就这么返回，Day06 讲过。

## 什么时候 panic，什么时候用 Result

| 场景 | 选择 |
|---|---|
| 示例、原型、测试 | `unwrap` / `expect`，省事 |
| 违反了编程契约（传进来一个根本不可能的值） | `panic!`，这是调用方的 bug |
| 错误是预期内的（用户输入非法、文件不存在） | 返回 `Result` |
| 一个函数可能因为好几种原因失败 | `Result` 配 `?`，交给上层决定 |

一般原则：默认返回 `Result`，`panic!` 留给本来就「不该发生」的 bug。

## 今日小结

- 不可恢复的错误用 `panic!` 崩掉；可恢复的错误用 `Result<T, E>` 返回
- `RUST_BACKTRACE=1 cargo run` 能打出调用栈，从上往下找第一个属于自己文件的栈帧
- `unwrap()` / `expect("...")` 是「失败就 panic」的简写，适合原型和测试
- `?` 运算符：`Ok` 解包继续，`Err` 提前 return，还会自动转换错误类型
- `?` 只能用在返回 `Result`（或 `Option`）的函数里；要用在 `main` 里得把签名改成 `-> Result<(), Box<dyn Error>>`

## 动手任务

1. 写一个 `fn read_note() -> Result<String, std::io::Error>`，用 `std::fs::read_to_string` 读一个存在的文件，成功就返回内容。
2. 把文件名换成不存在的，在 `main`（签名改成返回 `Result`）里用 `?` 把错误传出去，看程序打印什么。
3. 对 `File::open("x.txt")` 分别用 `unwrap()` 和 `expect("打开 x.txt 失败")`，对比两种 panic 信息差在哪。

> 第 2 题提示：`main` 返回 `Result` 时，程序最后会打印一行 `Error: ...`，那就是从 `?` 一路上来的错误。
> 第 3 题提示：`x.txt` 不存在时两次都会崩，所以两行别写在一起，写完一行跑一次才能看到各自的 panic 信息。

**配合阅读**：[官方书·第9章 panic! 与不可恢复的错误](https://rustwiki.org/zh-CN/book/ch09-01-unrecoverable-errors-with-panic.html)
