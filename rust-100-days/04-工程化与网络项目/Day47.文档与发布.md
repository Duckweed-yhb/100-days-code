# Day47 文档与发布

> 目标：学会写规范的文档注释、用 `cargo doc` 生成 HTML 文档，并把自己的库发布到 crates.io 让别人能 `cargo install` 或 `use`。

## 为什么文档和发布很重要

写代码是给自己看的，写文档是给别人（包括三个月后的自己）看的。Rust 有一个特别棒的传统：**文档和代码在一起，而且文档里的示例代码会被自动当测试跑**。这样文档永远不会过时——因为示例跑不通就编译失败。

今天就讲三件事：怎么写文档注释、怎么生成文档、怎么把库发到 crates.io。

## 文档注释：`///` 与 `//!`

Rust 有两种文档注释：

| 写法 | 写在哪 | 描述谁 |
|---|---|---|
| `///` | 函数/结构体/枚举**上方** | 描述它下面那个项怎么用 |
| `//!` | 文件或模块**开头**（内部） | 描述整个 crate 或模块本身 |

生活比喻：`///` 像贴在单个物品上的标签（“这是插座，往上插”）；`//!` 像整个房间门口的导览牌（“本房间是厨房，有灶台和冰箱”）。

```rust
//! # My Math Crate
//!
//! `my_math` 提供一组简单的数学工具函数。
//! 这是 crate 根的文档，用 `//!` 写在 lib.rs 最顶部。

/// 给一个整数加一。
///
/// # 示例
///
/// ```
/// use my_math::add_one;
/// assert_eq!(add_one(5), 6);
/// ```
pub fn add_one(x: i32) -> i32 {
    x + 1
}
```

注意 `///` 里的示例代码块用三个反引号包起来，和 Markdown 一模一样。**这段代码在 `cargo test` 时会被自动编译并运行**——这就是文档测试（doc test），今天先有个印象，Day57 会细讲。

### 常用文档小节惯例

社区约定俗成用这些标题，让文档结构统一：

```rust
/// # 示例
/// # Panics
/// # Errors
/// # Safety
```

- `# Examples`：怎么用（最重要，必写）
- `# Panics`：什么情况下会 panic
- `# Errors`：什么情况下返回 Err
- `# Safety`：unsafe 函数写安全约束

## 生成文档：cargo doc

```powershell
cargo doc              # 生成 HTML 到 target/doc/
cargo doc --open       # 生成并自动用浏览器打开
```

生成的文档长这样：左边是模块树，右边是函数签名、文档注释、示例。和你在 docs.rs 上看到的页面一模一样——因为 docs.rs 就是拿 `cargo doc` 生成的。

运行下面命令看看你自己项目的文档：

```powershell
cargo doc --open
```

## pub use：重导出让 API 更扁平

内部模块可能拆得很深，但用户不该写一长串路径。比如你的库内部是 `my_crate::kinds::PrimaryColor`，但希望用户直接 `use my_crate::PrimaryColor`。这就是**重导出（re-export）**：

```rust
// src/lib.rs
pub use kinds::PrimaryColor;   // 把深层类型提到 crate 根
pub use kinds::SecondaryColor;
pub use utils::mix;

pub mod kinds {
    pub enum PrimaryColor { Red, Yellow, Blue }
    pub enum SecondaryColor { Orange, Green, Purple }
}

pub mod utils {
    use crate::kinds::*;
    pub fn mix(_c1: PrimaryColor, _c2: PrimaryColor) -> SecondaryColor {
        SecondaryColor::Orange
    }
}
```

用户侧就简洁了：

```rust
use my_crate::{PrimaryColor, mix};  // 而不是 my_crate::kinds::PrimaryColor
```

> 经验：对外暴露的 API 要尽量扁平、直观。内部怎么分层是你的事，用户不该关心。

## 发布到 crates.io

crates.io 是 Rust 的官方包仓库（类似 npm registry、PyPI）。把库发上去，别人就能在 `Cargo.toml` 里写 `xxx = "0.1"` 依赖你。

### 发布前的准备

第一步：注册账号。去 https://crates.io 用 GitHub 登录，在 Account Settings 里生成一个 API token。

第二步：本地登录：

```powershell
cargo login <你的token>
```

token 存在 `~/.cargo/credentials.toml`，**绝对不要把这个文件提交到 git**。

第三步：在 `Cargo.toml` 填必填元数据。注意：`description` 和 `license` 是必填的，缺了不让发：

```toml
[package]
name = "my_math"                  # crates.io 上全局唯一，先到先得
version = "0.1.0"
authors = ["你的名字 <you@example.com>"]
description = "一个简单的数学工具库：加一、平方。"
license = "MIT OR Apache-2.0"     # 多个协议用 OR 分隔
repository = "https://github.com/你/my_math"  # 建议填
readme = "README.md"              # 建议填
```

### 发布

```powershell
cargo publish
```

它会自动打包、上传，并在 crates.io 上生成文档（`cargo doc` 的结果会挂到 docs.rs）。

### 几个重要规则

1. **版本一旦发布就不能改、不能删**。某个版本上传后就是永久的，哪怕代码有 bug，你也只能发新版本修。
2. **发新版**：改 `Cargo.toml` 里的 `version`（遵循语义化版本 SemVer：破坏兼容升 major，新功能升 minor，修 bug 升 patch），再 `cargo publish`。
3. **撤下有问题的版本**用 **yank**：它不删代码，只是禁止新项目依赖它，已经锁了版本的老项目不受影响。

```powershell
cargo yank --vers 0.1.1          # 撤回 0.1.1
cargo yank --vers 0.1.1 --undo   # 后悔了，撤销撤回
```

## cargo install：安装别人的命令行工具

这和在 `Cargo.toml` 里加依赖是两回事：

- **加依赖**：把库编译进你自己的项目
- **cargo install**：从 crates.io 下载一个**二进制 crate**，编译后装成独立命令，放到 `~/.cargo/bin/`

```powershell
cargo install ripgrep    # 装完就能直接用 rg 命令
cargo install bat        # 语法高亮的 cat
```

以后你自己写的命令行工具发布到 crates.io 后，别人一行 `cargo install 你的包名` 就能装上用。

## 今日小结

- 文档注释：`///` 写在项上方描述它，`//!` 写在容器开头描述整个 crate/模块
- `cargo doc --open` 生成 HTML 文档；`///` 里的示例代码会被 `cargo test` 当测试跑
- `pub use 深层路径::项;` 重导出，让对外 API 更扁平
- 发布流程：`cargo login` → 填 `description`/`license` → `cargo publish`；版本永久不可改，用 `cargo yank` 撤回坏版本
- `cargo install 包名` 把别人的二进制工具装成命令

## 动手任务

1. 给你之前写的某个库函数加上 `///` 文档注释和 `# Examples` 示例，跑 `cargo doc --open` 看看效果。
2. 在 lib.rs 顶部加一段 `//!` crate 级文档，描述这个库是干嘛的。
3. 选一个你常用的命令行工具（比如 `ripgrep`），用 `cargo install ripgrep` 装一下，然后在 PowerShell 里跑 `rg --version` 验证。

> 提示：如果只是想练手发布流程但不想真的上传，可以在本地试跑 `cargo publish --dry-run`，它会检查元数据是否齐全但不真正上传。

**配合阅读**：[官方书·14.2 将 crate 发布到 Crates.io](https://rustwiki.org/zh-CN/book/ch14-02-publishing-to-crates-io.html) · [crates.io 官网](https://crates.io)
