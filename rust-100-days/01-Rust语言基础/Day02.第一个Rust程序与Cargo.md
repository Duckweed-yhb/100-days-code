# Day02 第一个 Rust 程序与 Cargo

> **前置知识**：Day01 装好 rustup，`rustc --version` 能输出版本号
> **难度**：入门
> **预计阅读时间**：20 分钟
> **代码说明**：本章两块 Rust 代码各自是一个完整的 `main.rs`，替换进去就能跑，不用拼在一起（两块都带 `fn main`，拼起来会撞车）。
> 每块前面标了 `📦`（能单独跑）。

> 目标：亲手写出你的第一个 Rust 程序，并学会用 Cargo 创建、编译、运行一个标准项目。

---

## 写一个 Hello World

在桌面或任意目录新建一个文件，命名为 `main.rs`（Rust 源码文件的后缀是 `.rs`），敲入这段代码：

📦 本块能单独跑（就是下面 `rustc main.rs` 要编的那个文件）：

```rust
fn main() {
    println!("Hello, world!");
}
```

打开 PowerShell，`cd` 到这个文件所在目录，用 `rustc` 直接编译，然后运行：

```powershell
rustc main.rs
.\main.exe
```

`.\` 的意思是「当前目录下的这个文件」。Windows 默认不会去当前目录找程序，所以不能只写 `main.exe`。

屏幕上会打印出 `Hello, world!`。这就是你的第一个 Rust 程序。

## 逐行拆解这个程序

### `fn main()`：程序的入口

- `fn` 是 function（函数）的缩写，用来定义一个函数。函数是一包可以反复调用的代码。
- `main` 这个名字是**固定的**：程序启动后从 `main` 里第一条语句开始执行，每个能跑的程序都必须有一个 `main`。
- 后面的 `()` 是参数列表，里面空的表示这个函数不接收任何参数。
- 花括号 `{ }` 包住的是函数体，也就是真正要执行的代码。

### `println!("Hello, world!");`：打印一行

- `println!` 后面那个 `!` 表示它是一个**宏（macro）**，不是普通函数。
- 它把括号里的内容打印到屏幕，并且**自动换行**（名字里的 `ln` 是 line，行）。
- 要打印的文字用**双引号** `"..."` 包起来。
- 行尾的 `;`（分号）表示这一条语句结束。Rust 里绝大多数语句都要写分号。

## 宏和函数有什么区别

新手最常问的是：为什么 `println!` 后面要加个 `!`？

| 对比项 | 普通函数 | 宏（带 `!`） |
|---|---|---|
| 写法 | `add(a, b)` | `println!("...")` |
| 处理时机 | 运行时才调用 | **编译时**先展开成代码 |
| 参数个数 | 定义时就固定 | 可以变，0 个、1 个、多个都行 |
| 本质 | 一段封装好的逻辑 | 一个代码模板，按需生成代码 |

打印之所以要用宏，是因为它得接受任意数量的参数：

📦 本块能单独跑：

```rust
fn main() {
    println!("只打一行");
    println!("一个数字：{}", 42);
    println!("两个数：{} 和 {}", 3, 5);
}
```

这里有两个第一次出现的东西，顺手解释掉：

- 字符串里的 `{}` 是**占位符**，运行时被后面依次传进来的值替换。它跟上面包函数体的花括号 `{ }` 是两码事，一个在字符串里面，一个在代码里。
- 参数个数能随便变，普通函数做不到，所以它必须写成宏。**看到名字后面跟着 `!`，就是宏。**

（Day05 讲逻辑运算符还会出现一个 `!`，那个是「取反」，跟这里的宏标记不是一回事，到时候会区分。）

## rustc 直接编译 vs Cargo 项目

刚才 `rustc main.rs` 一条命令就编完了。这跟用 gcc 直接编一个单文件 C 程序是一回事：小程序没问题，项目一大、又要引第三方库，手动管编译参数和依赖就很痛苦。

所以 Rust 官方给了统一的构建工具 **Cargo**。以后你几乎不会直接用 `rustc`，都是用 `cargo`。

## 认识 Cargo

Cargo 是 Rust 的**构建系统和包管理器**，它帮你：

- 一键创建项目骨架
- 编译、运行代码
- 下载并管理第三方依赖（Rust 里一个库叫一个 **crate**，准确说是「一个编译单元」，Day21 讲模块系统时会说清）
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

`cargo new` 自动生成了一整套项目结构：

```
hello_cargo/
├── Cargo.toml
├── .gitignore
└── src/
    └── main.rs
```

- `src/main.rs`：主程序文件，Cargo 已经帮你写好了 Hello World。
- `Cargo.toml`：项目配置文件（TOML 是这种配置文件的格式名），记项目名、版本、依赖等。
- `.gitignore`：`cargo new` 顺手帮你 `git init` 了一个仓库，这个文件告诉 Git 哪些东西不用管。

### Cargo.toml 长什么样

打开 `Cargo.toml`，默认内容大概是：

```toml
[package]
name = "hello_cargo"
version = "0.1.0"
edition = "2024"

[dependencies]
```

- `[package]` 这一段配当前这个包：名字、版本、用哪一版 Rust。`edition` 是「版次」，指这套代码按哪一年的语法规则编。它由你的 Cargo 版本决定：新装的 Cargo（1.91 就是）默认写 `"2024"`，早几年的版本写 `"2021"`。以你机器上生成的那份为准，别照抄这里的数字。
- `[dependencies]` 这一段以后填你依赖的第三方库，现在是空的。

### 常用的三个 cargo 命令

| 命令 | 作用 | 什么时候用 |
|---|---|---|
| `cargo build` | 编译项目，生成可执行文件 | 想要产物（在 `target\debug\` 下） |
| `cargo run` | **编译并直接运行** | 日常开发最常用 |
| `cargo check` | 只检查能不能编译，**不生成可执行文件** | 写代码时快速验证，最快 |

在项目目录里运行：

```powershell
cargo run
```

输出大概长这样：

```
   Compiling hello_cargo v0.1.0 (...)
    Finished `dev` profile [unoptimized + debuginfo] target(s) in 0.40s
     Running `target\debug\hello_cargo.exe`
Hello, world!
```

第一次编译会慢一点。它还要生成 `Cargo.lock`（记下每个依赖的精确版本，**别手动改它**）和 `target/` 目录（编译产物都扔在这里，整个删掉也没事，重编会再生成）。

> 写代码时多用 `cargo check`，秒级出结果，能马上发现语法错误；真要跑起来再 `cargo run`。

### 调试构建 vs 发布构建

Cargo 默认做 **debug（调试）构建**：编译快、带调试信息，方便开发。程序写好要发布给别人用时换：

```powershell
cargo build --release
```

产物会放到 `target\release\` 目录。它编译更慢，但运行速度快很多，因为开了优化。**做性能测试（benchmark）一定要用 `--release` 编出来的版本。**

## 今日小结

- 最小 Rust 程序：`fn main() { println!("..."); }`，`main` 是入口，`println!` 是宏。
- 名字后面带 `!`（如 `println!`）就是宏，编译时展开，参数个数可变。
- 直接编译用 `rustc main.rs`，但正式项目都用 Cargo。
- Cargo 三件套：`cargo build`（编译）、`cargo run`（编译并运行）、`cargo check`（快速检查）。
- 项目源码放 `src/`，配置在 `Cargo.toml`，产物在 `target/`，发布用 `cargo build --release`。

## 动手任务

1. 用 `cargo new my_first` 创建一个新项目，`cd` 进去后把 `src/main.rs` 里的打印内容改成你自己的名字，用 `cargo run` 跑出来。
2. 先只跑一遍 `cargo check`，再跑 `cargo run`，对比两者输出：`check` 不生成 exe，只告诉你编不编得过。
3. 故意把 `println!` 末尾的 `!` 删掉写成 `println("hi")`，编译。你会看到：

   ```
   error[E0423]: expected function, found macro `println`
    --> src\main.rs:2:5
     |
   2 |     println("hi");
     |     ^^^^^^^ not a function
     |
   help: use `!` to invoke the macro
   ```

   编译器直接说明白了：这不是函数，是宏，加 `!` 才能调。看懂再改回来。

---

**配合阅读**：[官方书·第1章 Hello World 与 Hello, Cargo!](https://rustwiki.org/zh-CN/book/ch01-02-hello-world.html)
