# Day01 认识 Rust 与环境安装

> 目标：搞清楚 Rust 是什么、为什么值得学，并在你的电脑上把开发环境装好、验证可用。

## Rust 是什么

Rust 是一门**系统级编程语言**，最早由 Mozilla 的 Graydon Hoare 发起，2015 年发布 1.0，现在由 Rust 基金会维护。它最想解决的问题是：**既要 C/C++ 那样的性能和底层控制力，又要内存安全，还不想靠垃圾回收（GC）**。

它靠一套叫**所有权（ownership）+ 借用检查（borrow checker）**的机制，在**编译期**就把空指针、数据竞争等常见内存错误挡下来。换句话说：别的语言很多 bug 要等运行时才炸，Rust 在你编译时就逼你改好。

三大特点：

| 特点 | 大白话 |
|---|---|
| 内存安全 | 编译器帮你管住内存，不会出现野指针、悬垂引用 |
| 零成本抽象 | 用高层语法（迭代器、泛型）写，编译后和手写底层一样快 |
| 无畏并发 | 多线程代码编译器帮你检查，不容易写出数据竞争 |

典型用途：操作系统 / 嵌入式、后端服务、命令行工具、WebAssembly、游戏图形、区块链、数据库与云原生基础设施。

## 安装 Rust

Rust 官方用 **rustup** 来安装和管理 Rust 工具链，它会一并装好：

- `rustc`：Rust 编译器
- `cargo`：包管理 + 构建工具（你以后最常用的命令）
- 标准库文档等

### Windows 安装（重点，容易踩坑）

**第一步：先装 Visual Studio 的 C++ 构建工具**

Rust 在 Windows 上默认用 MSVC 工具链，需要微软的链接器 `link.exe`。注意：**VS Code 不等于 Visual Studio**，光装 VS Code 不够。

1. 下载「Build Tools for Visual Studio」：https://visualstudio.microsoft.com/visual-cpp-build-tools/
2. 运行安装器，勾选 **「使用 C++ 的桌面开发」(Desktop development with C++)**
3. 安装（体积较大，耐心等待）

> 如果跳过这步，后面编译会报 `linker 'link.exe' not found`。

**第二步：安装 rustup**

1. 打开 https://www.rust-lang.org/zh-CN/tools/install
2. 下载 `rustup-init.exe` 并运行
3. 看到提示后按回车，选默认安装（`1) Proceed with installation`）
4. 安装完成后，**重新打开**一个终端窗口（让环境变量生效）

### 验证安装

打开 PowerShell 或 CMD，运行：

```powershell
rustc --version
cargo --version
rustup --version
```

能打印出版本号（例如 `rustc 1.9x.x`）就说明装好了。运行下面命令看看你的输出：

```powershell
rustc --version
```

## 配置编辑器（VS Code）

推荐用 VS Code，装这几个扩展：

1. **rust-analyzer**（必装）：补全、跳转、实时报错、格式化；在 `fn main` 上方还会出现可点击的 `Run` / `Debug`
2. **Even Better TOML**：让 `Cargo.toml` 有高亮
3. **Error Lens**（可选）：把错误直接显示在出错的代码行旁边

## 本地官方文档

安装后，本机就有一份完整的官方书和标准库文档，无需联网：

```powershell
rustup doc
```

会自动用浏览器打开。想查某个标准库类型（比如 `String`）时特别好用。

## 常用 rustup 命令速览

| 命令 | 作用 |
|---|---|
| `rustup update` | 更新 Rust 到最新稳定版 |
| `rustup self uninstall` | 卸载 Rust |
| `rustup doc` | 打开本地文档 |
| `rustup show` | 查看当前工具链 |

## 今日小结

- Rust = 高性能 + 内存安全（靠所有权，不靠 GC）的系统级语言
- Windows 安装顺序：**先 VS C++ Build Tools，再 rustup**
- 装好后有 `rustc`、`cargo`，用 `--version` 验证
- VS Code 配 rust-analyzer，`rustup doc` 看本地文档

## 动手任务

1. 确认 `rustc --version`、`cargo --version` 都能输出版本号
2. 运行一次 `rustup doc`，看看本地文档长什么样
3. 装好 VS Code 的 rust-analyzer 扩展

> 遇到 `link.exe not found` 之类报错，把完整报错发我。

**配合阅读**：[官方书·第1章 安装](https://rustwiki.org/zh-CN/book/ch01-01-installation.html)
