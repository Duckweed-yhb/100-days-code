# Day01 认识 Rust 与环境安装

> **前置知识**：无，从零开始
> **难度**：入门
> **预计阅读时间**：10 分钟
> **代码说明**：本章没有 Rust 代码，只有装完环境后要敲的几条命令。命令框里的内容复制到 PowerShell 里执行就行，不用建任何文件。

> 目标：搞清楚 Rust 是什么、为什么值得学，并在你的电脑上把开发环境装好、验证可用。

---

## Rust 是什么

Rust 是一门**系统级编程语言**，最早由 Mozilla 的 Graydon Hoare 发起，2015 年发布 1.0，现在由 Rust 基金会维护。它想同时拿到两样东西：C/C++ 那样的性能和底层控制力，以及内存安全，而且不用垃圾回收（GC，garbage collection，自动回收不再使用的内存的机制）。

它靠一套叫**所有权（ownership）+ 借用检查（borrow checker）**的机制，在**编译期**把空指针、数据竞争这类内存错误挡下来。别的语言里很多 bug 要等程序跑起来才炸，Rust 在你编译的时候就拦住你。这套机制从 Day10 开始讲，今天知道有这么回事就够。

三大特点：

| 特点 | 大白话 |
|---|---|
| 内存安全 | 编译器帮你管住内存，不会出现野指针（指向已经失效的内存）和悬垂引用（引用指向的数据已经被释放） |
| 零成本抽象 | 用迭代器、泛型这类高层写法，编译后和手写底层代码一样快 |
| 无畏并发 | 多线程代码编译器会替你检查，不容易写出数据竞争 |

典型用途：操作系统 / 嵌入式、后端服务、命令行工具、WebAssembly、游戏图形、区块链、数据库与云原生基础设施。

## 安装 Rust

Rust 官方用 **rustup** 来装和管理 Rust。rustup 是个安装器，也是一个版本管理器：装了它，以后升级版本、切换版本都靠它。它会一并装好下面这些（合称**工具链**，指编译器加配套工具的一整套）：

- `rustc`：Rust 编译器，把 `.rs` 文件编成可执行文件
- `cargo`：构建工具 + 包管理器，你以后最常敲的命令
- 标准库文档等

### Windows 安装

**第一步：先装 Visual Studio 的 C++ 构建工具**

Rust 在 Windows 上默认用 MSVC 工具链，需要微软的链接器 `link.exe`。链接器是编译的最后一步，负责把编译出来的目标文件拼成一个能运行的程序。注意：**VS Code 不等于 Visual Studio**，光装 VS Code 不够。

1. 下载「Build Tools for Visual Studio」：https://visualstudio.microsoft.com/visual-cpp-build-tools/
2. 运行安装器，勾选 **「使用 C++ 的桌面开发」(Desktop development with C++)**
3. 安装（体积较大，耐心等）

> 跳过这步，后面编译会报 `linker 'link.exe' not found`。

**第二步：安装 rustup**

1. 打开 https://www.rust-lang.org/zh-CN/tools/install
2. 下载 `rustup-init.exe` 并运行
3. 看到提示后按回车，选默认安装（`1) Proceed with installation`）
4. 装完**重新打开**一个终端窗口，让环境变量生效

### 验证安装

打开 PowerShell 或 CMD，三条命令都跑一遍：

```powershell
rustc --version
cargo --version
rustup --version
```

每条都打印出版本号（例如 `rustc 1.9x.x`）就说明装好了。只出来一条、另外两条报「不是内部或外部命令」，多半是没重开终端。

## 配置编辑器（VS Code）

推荐用 VS Code，装这几个扩展：

1. **rust-analyzer**（必装）：补全、跳转、实时报错、格式化；在 `fn main` 上方还会出现可点击的 `Run` / `Debug`（`fn main` 是每个 Rust 程序的入口函数，Day02 细讲）
2. **Even Better TOML**：让 `Cargo.toml` 有高亮（`Cargo.toml` 是项目配置文件，Day02 会建一个）
3. **Error Lens**（可选）：把错误直接显示在出错那行代码旁边

## 本地官方文档

安装后本机就有一份完整的官方书和标准库文档，不用联网：

```powershell
rustup doc
```

它会用浏览器打开。想查某个标准库类型（比如 `String`）时特别好用。

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

---

**配合阅读**：[官方书·第1章 安装](https://rustwiki.org/zh-CN/book/ch01-01-installation.html)
