# Day02 第一个 Rust 程序与 Cargo

> 目标：亲手写出你的第一个 Rust 程序，并学会用 Cargo 创建、编译、运行一个标准项目。

## 写一个 Hello World

还记得 Day01 我们用 `rustc --version` 验证过 Rust 装好了。今天就让它真正跑起来。

先在桌面或任意目录新建一个文件，命名为 `main.rs`（Rust 源码文件后缀是 `.rs`），敲入下面这段代码：

```rust
fn main() {
    println!("Hello, world!");
}
```

打开 PowerShell，`cd` 到这个文件所在目录，然后用 `rustc` 直接编译：

```powershell
rustc main.rs
.\main.exe
```

运行下面命令看看输出：

```powershell
.\main.exe
```

你会看到屏幕上打印出 `Hello, world!`。恭喜，这是你的第一个 Rust 程序。

## 逐行拆解这个程序

### `fn main()` —— 程序的入口

- `fn` 是 **function（函数）** 的缩写，用来定义一个函数。
- `main` 这个名字是**固定的**：程序启动时会自动从 `main` 开始执行，每个可执行程序都必须有它。
- 后面的 `()` 表示这个函数**不接收任何参数**。
- 花括号 `{ }` 包住函数体。

### `println!("Hello, world!");` —— 打印一行

- `println!` 不是普通函数，注意末尾的 `!`——它表示这是一个**宏（macro）**。
- 作用：把括号里的内容打印到屏幕，并**自动换行**。
- 文本用**双引号** `"..."` 包起来。
- 行尾的 `;`（分号）表示这一条语句结束，Rust 里大多数语句都要写分号。

## 宏和函数有什么区别

新手最容易疑惑：为什么 `println!` 后面要加个 `!`？

| 对比项 | 普通函数 | 宏（以 `!` 结尾） |
|---|---|---|
| 写法 | `add(a, b)` | `println!("...")` |
| 处理时机 | 运行时才调用 | **编译时**先展开生成代码 |
| 参数个数 | 提前固定 | 可以变（0 个、1 个、多个都行） |
| 本质 | 封装一段逻辑 | 像"代码模板"，按需生成代码 |

为什么打印偏偏要用宏？因为 `println!` 能接受任意数量的参数：

```rust
println!("只打一行");                    // 0 个占位符
println!("一个数字：{}", 42);            // 1 个占位符
println!("两个数：{} 和 {}", 3, 5);      // 2 个占位符
```

这种"参数个数随便变"的能力，普通函数做不到，所以它必须是宏。**记住：看到名字后面跟着 `!`，就是宏。**

> `{}` 是占位符，运行时会被后面的值依次替换。这个后面天天用。

## rustc 直接编译 vs Cargo 项目

刚才我们用 `rustc main.rs` 一条命令就编译了。这就像用gcc直接编一个单文件C程序——小程序没问题，但一旦项目变大、依赖第三方库，手动管理编译参数和依赖就会非常痛苦。

所以 Rust 官方给了一个统一的构建工具：**Cargo**。以后你几乎不会直接用 `rustc`，都是 `cargo`。

## 认识 Cargo

Cargo 是 Rust 的**构建系统和包管理器**，它帮你：

- 一键创建项目骨架
- 编译、运行代码
- 下载并管理第三方依赖（在 Rust 里第三方库叫 **crate**）
- 生成文档

先确认它装好了：

```powershell
cargo --version
```

### 创建第一个 Cargo 项目

在你想放代码的目录下打开 PowerShell，运行：

```powershell
cargo new hello_cargo
cd hello_cargo
```

`cargo new` 帮你自动生成了一整套项目结构：

```
hello_cargo/
├── Cargo.toml
├── .gitignore
└── src/
    └── main.rs
```

- `src/main.rs`：你的主程序文件，Cargo 已经帮你写好了 Hello World。
- `Cargo.toml`：项目配置文件（TOML 格式），记录项目名、版本、依赖等。
- `.gitignore`：因为 `cargo new` 还顺手帮你初始化了一个 Git 仓库。

### Cargo.toml 长什么样

打开 `Cargo.toml`，默认内容大概是：

```toml
[package]
name = "hello_cargo"
version = "0.1.0"
edition = "2021"

[dependencies]
```

- `[package]` 这一段配置当前这个包：名字、版本、用的 Rust 版本（`edition`）。
- `[dependencies]` 这一段以后用来填你依赖的第三方库，现在是空的。

### 常用的三个 cargo 命令

| 命令 | 作用 | 什么时候用 |
|---|---|---|
| `cargo build` | 编译项目，生成可执行文件 | 想要产物（在 `target\debug\` 下） |
| `cargo run` | **编译并直接运行** | 日常开发最常用 |
| `cargo check` | 只检查能不能编译，**不生成可执行文件** | 写代码时快速验证，速度最快 |

在项目目录里运行：

```powershell
cargo run
```

你会看到类似输出：

```
Compiling hello_cargo v0.1.0 (...)
Finished dev [unoptimized + debuginfo] target(s)
     Running `target\debug\hello_cargo.exe`
Hello, world!
```

第一次编译会慢一点，因为它要生成 `Cargo.lock`（记录依赖的精确版本，**别手动改它**）和 `target/` 目录（编译产物都在这）。

> 经验之谈：写代码过程中频繁用 `cargo check`，秒级出结果，能快速发现语法错误；要真正跑起来再 `cargo run`。

### 调试构建 vs 发布构建

Cargo 默认是 **debug（调试）构建**：编译快、带调试信息，方便开发。等你程序写好要发布给别人用时，用：

```powershell
cargo build --release
```

产物会放到 `target\release\` 目录。它编译更慢，但运行速度快很多——因为开了优化。**做性能测试（benchmark）一定要用 `--release` 的版本。**

## 今日小结

- 最小 Rust 程序：`fn main() { println!("..."); }`，`main` 是入口，`println!` 是宏。
- 名字后面带 `!`（如 `println!`）就是宏，编译时展开，参数个数可变。
- 直接编译用 `rustc main.rs`，但正式项目都用 Cargo。
- Cargo 三件套：`cargo build`（编译）、`cargo run`（编译并运行）、`cargo check`（快速检查）。
- 项目源码放 `src/`，配置在 `Cargo.toml`，产物在 `target/`，发布用 `cargo build --release`。

## 动手任务

1. 用 `cargo new my_first` 创建一个新项目，`cd` 进去后把 `src/main.rs` 里的打印内容改成你自己的名字，用 `cargo run` 跑出来。
2. 试试只跑 `cargo check`，观察它和 `cargo run` 输出的区别（一个不生成 exe，一个生成并运行）。
3. 故意把 `println!` 末尾的 `!` 删掉写成 `println(...)`，看看编译器报什么错，体会"宏 vs 函数"的区别，然后改回来。

**配合阅读**：[官方书·第1章 Hello World 与 Hello, Cargo!](https://rustwiki.org/zh-CN/book/ch01-02-hello-world.html)
