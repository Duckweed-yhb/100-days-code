# Day46 Cargo 进阶与工作空间

> 目标：搞懂 Cargo 的发布配置（profile）、怎么用工作空间（workspace）管理多个互相依赖的 crate，以及怎么用特性（feature）按需开关功能。

## 为什么需要今天这些内容

你已经会 `cargo new`、`cargo build`、`cargo run` 了。但真实项目里你会遇到三个问题：

1. **开发时想编译快，发布时想运行快**——两套配置怎么切？
2. **一个项目想拆成好几个 crate**（比如一个库 + 一个命令行工具 + 一个示例），它们共享依赖、不想重复编译，怎么组织？
3. **一个库想给不同用户提供不同功能**（比如“要不要加数据库支持”“要不要加密”），怎么做到按需编译、不把不需要的代码编进去？

这三件事，分别对应今天的三个主角：**profile**、**workspace**、**feature**。

## 发布配置 profile：dev 与 release

Cargo 内置两套编译配置：

| 命令 | 用哪套 profile | 特点 |
|---|---|---|
| `cargo build` | `dev` | 不优化（`opt-level = 0`），编译快，带调试信息 |
| `cargo build --release` | `release` | 全力优化（`opt-level = 3`），运行快，编译慢 |

生活比喻：开发模式像在草稿纸上打草稿——画得快、不追求工整；发布模式像正式交卷——只交一次，要写得漂亮。你一天要编译几十次，所以草稿模式优先快；发布产物要跑几万次，所以优先快。

在 `Cargo.toml` 里可以覆盖默认值：

```toml
# Cargo.toml

[profile.dev]
opt-level = 0   # 开发模式：不优化，编译快

[profile.release]
opt-level = 3   # 发布模式：全力优化
lto = true      # 链接时优化（Link Time Optimization），进一步减小体积、提速
codegen-units = 1  # release 模式建议设为 1，配合 lto 优化更充分
```

常用配置项：

| 配置项 | 作用 |
|---|---|
| `opt-level` | 优化级别 0~3，越高编译越慢、运行越快 |
| `debug = true/false` | 是否生成调试信息（发布包默认 false） |
| `lto = true` | 跨 crate 链接时优化，产物更小更快，但编译更慢 |
| `panic = "abort"` | 发布到嵌入式等场景时，遇到 panic 直接终止而不展开栈 |

运行下面命令看看 release 产物（在 `target/release/` 而不是 `target/debug/`）：

```powershell
cargo build --release
```

## 工作空间 workspace：管理多个 crate

当一个项目变大，你会想拆成多个 crate。比如：

- `my_lib`：核心逻辑（库，给别人 `use` 的）
- `my_cli`：命令行入口（二进制，调 `my_lib`）
- `my_web`：Web 服务（也调 `my_lib`）

如果各自独立，它们会重复编译 `my_lib`、各有一份 `Cargo.lock` 和 `target/`，浪费磁盘和时间。**工作空间（workspace）**就是让多个 crate 共享一份 `Cargo.lock` 和一个 `target/` 目录。

### 怎么建工作空间

在根目录放一个只写 `[workspace]` 的 `Cargo.toml`：

```toml
# Cargo.toml（根目录，注意：它自己没有 [package]）
[workspace]
members = [
    "my_lib",    # 库 crate
    "my_cli",    # 二进制 crate
]
```

然后创建两个成员：

```powershell
cargo new my_cli          # 二进制 crate（有 src/main.rs）
cargo new my_lib --lib    # 库 crate（有 src/lib.rs）
```

目录结构长这样：

```
my_workspace/
├── Cargo.lock        # 整个工作空间共享一份
├── Cargo.toml        # [workspace] 配置在这里
├── target/           # 所有成员共享一个 target
├── my_cli/
│   ├── Cargo.toml
│   └── src/main.rs
└── my_lib/
    ├── Cargo.toml
    └── src/lib.rs
```

### 成员之间怎么互相调用

`my_cli` 要用 `my_lib`，在它自己的 `Cargo.toml` 里声明路径依赖：

```toml
# my_cli/Cargo.toml
[dependencies]
my_lib = { path = "../my_lib" }
```

然后在 `my_cli/src/main.rs` 里正常 `use my_lib;` 即可。

### 常用命令

```powershell
cargo build              # 构建整个工作空间所有成员
cargo run -p my_cli      # -p（package）指定跑哪个包
cargo test -p my_lib     # 只跑 my_lib 的测试
cargo doc --open         # 给整个工作空间生成文档
```

> 注意：工作空间里每个 crate 发布到 crates.io 时要**单独** `cargo publish`，没有“一键全发”。

## 特性 feature：按需开关功能

**特性（feature）**是一种“条件编译开关”。生活比喻：买手机时选“要不要 5G”“要不要长焦镜头”——不选的配置就不装进去，省成本。库作者可以在 `Cargo.toml` 里定义一组可选功能，用户按需开启。

### 定义 feature

```toml
# my_lib/Cargo.toml
[package]
name = "my_lib"
version = "0.1.0"

[features]
# 默认开启的功能（用户可以 default-features = false 关掉）
default = ["std"]
std = []              # 一个空 feature，表示“启用标准库支持”

# 可选功能：开启后才引入对应的依赖
regex = ["dep:regex"]   # 开启 regex feature 时，才引入 regex 这个依赖
crypto = ["dep:ring"]
```

### 在代码里条件编译

用 `#[cfg(feature = "regex")]` 标注只有开启该 feature 才编译的代码：

```rust
// my_lib/src/lib.rs

/// 在文本中搜索关键词
pub fn search(text: &str, keyword: &str) -> Vec<usize> {
    #[cfg(feature = "regex")]   // 开启 regex feature 时才用正则
    {
        // 这里用 regex crate 做更复杂的匹配
        vec![]
    }

    #[cfg(not(feature = "regex"))]  // 没开时退回最简单的字符串包含
    {
        text.match_indices(keyword).map(|(i, _)| i).collect()
    }
}
```

### 用户怎么选 feature

```toml
# 别人用你的库时
[dependencies]
my_lib = { version = "0.1", default-features = false, features = ["regex"] }
```

意思是：不要默认功能，只要 regex 这个功能。这样 `ring`（加密相关）就不会被编译进来。

命令行也能临时开启 feature 跑测试：

```powershell
cargo test --features regex,crypto
cargo check --no-default-features   # 检查关掉默认 feature 后还能不能编译
```

> 操作系统训练营里经常听到的 `no_std` 就是典型场景：库默认开 `std` feature，嵌入式用户用 `default-features = false` 关掉它，就能编译到没有操作系统的环境上。这就是 feature 的威力。

## 今日小结

- **profile**：`dev`（开发，编译快）和 `release`（发布，运行快）两套配置，在 `Cargo.toml` 的 `[profile.*]` 里调 `opt-level`、`lto` 等
- **workspace**：根目录 `Cargo.toml` 写 `[workspace] members = [...]`，多个 crate 共享 `Cargo.lock` 和 `target/`，用 `-p 包名` 指定操作哪个
- **feature**：`[features]` 定义可选功能，代码里用 `#[cfg(feature = "名字")]` 条件编译，用户按需 `features = [...]` 开启
- 三者分别解决“编译快还是运行快”“多 crate 怎么组织”“功能怎么按需开关”

## 动手任务

1. 把你之前写的猜数字游戏改造成一个工作空间：拆成 `guessing_lib`（库，放核心逻辑）和 `guessing_cli`（二进制，调库）。
2. 在 `Cargo.toml` 里给 release profile 加上 `lto = true`，分别 `cargo build` 和 `cargo build --release`，对比 `target/debug/` 和 `target/release/` 下可执行文件的大小。
3. 给 `guessing_lib` 定义一个 feature `debug-log`，开启后在猜数字时打印“提示偏大/偏小”的调试信息，用 `#[cfg(feature = "debug-log")]` 包起来。

> 提示：`cargo new my_lib --lib` 创建的库默认有 `src/lib.rs`；`-p` 是 `--package` 的缩写。

**配合阅读**：[官方书·14.1 采用发布配置](https://rustwiki.org/zh-CN/book/ch14-01-release-profiles.html) · [官方书·14.3 Cargo 工作空间](https://rustwiki.org/zh-CN/book/ch14-03-cargo-workspaces.html) · [Cargo 手册·Features 参考](https://doc.rust-lang.org/cargo/reference/features.html)
