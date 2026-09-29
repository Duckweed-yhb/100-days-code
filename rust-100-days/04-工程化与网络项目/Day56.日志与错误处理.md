# Day56 日志与错误处理

> 目标：学会用 `log` + `env_logger` 打日志，用 `thiserror` 给库定义规整的错误类型，用 `anyhow` 给应用快速处理任意错误——搞清这俩怎么分工。

## 为什么不用 println! 调试

小程序用 `println!` 打印状态还行，但项目一大就乱：

- 没法分级（哪些是重要错误，哪些是调试细节？）
- 没法开关（上线后不想看到调试信息，但也不想删代码）
- 全打到 stdout，和正常输出混在一起

Rust 的解法是 **log 门面 + 具体实现** 的组合：你写代码时调 `log` 这个 crate 定义的宏，运行时再决定日志打到哪、打哪个级别。

## log + env_logger

`log` 只定义了日志宏和级别，真正把日志写出来的是 `env_logger`（默认实现，读环境变量控制）。

### 添加依赖

```toml
# Cargo.toml
[dependencies]
log = "0.4"
env_logger = "0.11"
```

### 使用

```rust
use log::{debug, info, warn, error};

fn main() {
    // 程序一开始就初始化 logger（一般放第一行）
    env_logger::init();

    debug!("这是调试信息，默认不显示");
    info!("服务器启动");
    warn!("磁盘快满了");
    error!("连接失败: 超时");
}
```

日志从低到高分五级：

| 级别 | 用途 |
|---|---|
`trace` | 最细的追踪 |
`debug` | 调试细节 |
`info` | 正常进度信息 |
`warn` | 警告（还能继续跑） |
`error` | 错误（这件事失败了） |

### 控制日志级别

`env_logger` 读 `RUST_LOG` 环境变量决定打哪些。PowerShell 里：

```powershell
$env:RUST_LOG="info"; cargo run
```

- `RUST_LOG=debug`：显示 debug 及以上
- `RUST_LOG=info`：只显示 info/warn/error
- `RUST_LOG=error`：只显示错误
- `RUST_LOG=myapp=debug`：只把你自己 crate 的日志开到 debug，第三方库保持 info

不设 `RUST_LOG` 时默认只显示 `error`——这就是为什么你刚才直接 `cargo run` 啥也看不到。

> 这和 Day53 的 `eprintln!` 不冲突：`eprintln!` 是“给用户看的一行错误提示”，日志是“给开发者看的运行记录”。面向用户的 CLI 工具最后一行错误用 `eprintln!`，程序内部的过程记录用 `log`。

## thiserror：给库定义错误类型

Day53 里我们用过 `Box<dyn Error>`——简单，但对库来说太粗：调用方拿到一个错误，不知道是文件读失败还是参数非法，没法分别处理。

库应该定义自己的错误枚举，让调用方可以 `match` 不同错误做不同反应。手写这种枚举要实现 `Error` trait 很啰嗦，**thiserror** 用 derive 帮你生成。

### 添加依赖

```toml
[dependencies]
thiserror = "1.0"
```

### 定义错误

```rust
use thiserror::Error;

#[derive(Debug, Error)]
pub enum MyError {
    #[error("读取配置文件失败: {0}")]
    Io(#[from] std::io::Error),       // 包装 io::Error，#[from] 自动转换

    #[error("配置缺少字段: {0}")]
    MissingField(String),

    #[error("端口号非法: {0}")]
    InvalidPort(i32),
}
```

解释：

- `#[derive(Error)]` 自动帮你实现 `std::error::Error`
- `#[error("...")]` 定义错误的展示信息（Display），`{0}` 对应枚举变体里的字段
- `#[from]` 让 `From<io::Error>` 自动实现——于是你在函数里写 `?`，`io::Error` 会自动转成 `MyError::Io`

使用：

```rust
pub fn load_config() -> Result<String, MyError> {
    let text = std::fs::read_to_string("config.txt")?;  // io::Error 自动转 MyError::Io
    if text.is_empty() {
        return Err(MyError::MissingField("host".into()));
    }
    Ok(text)
}
```

调用方可以精确匹配：

```rust
match load_config() {
    Err(MyError::MissingField(f)) => eprintln!("缺字段: {}", f),
    Err(e) => eprintln!("其他错误: {}", e),
    Ok(c) => println!("配置: {}", c),
}
```

## anyhow：给应用快速处理任意错误

**应用（binary）**和库不同：你不需要让调用方区分错误类型（你就是最顶层），你只需要把错误信息漂亮地打印出来。这时候用 **anyhow**：

### 添加依赖

```toml
[dependencies]
anyhow = "1.0"
```

### 使用

```rust
use anyhow::{Result, Context};
use std::fs;

fn main() -> Result<()> {
    // context 给错误加一层上下文，出错时能看到“在哪一步失败的”
    let text = fs::read_to_string("config.txt")
        .context("读取 config.txt 失败")?;

    let port: i32 = text.trim()
        .parse()
        .context("端口号不是合法数字")?;

    println!("端口: {}", port);
    Ok(())
}
```

`anyhow::Result<T>` 就是 `Result<T, anyhow::Error>` 的别名。它的好处：

- 任何错误类型都能 `?` 进来（自动转成 `anyhow::Error`）
- `.context("...")` 给错误加注释，报错时能看到完整链路：`读取 config.txt 失败: No such file or directory (os error 2)`

## thiserror 和 anyhow 怎么分工

这是 Rust 社区的黄金法则：

| 场景 | 用什么 | 为什么 |
|---|---|---|
| 写**库**（lib.rs，给别人 use） | **thiserror** | 调用方需要 match 具体错误类型做分支处理 |
| 写**应用**（main.rs，最终用户跑的） | **anyhow** | 不需要区分错误类型，只要能打印出可读的错误信息 |

生活比喻：thiserror 像医院给每个病编了精确的诊断编码（方便后续治疗）；anyhow 像急诊室——先把人救下来，记一句“哪不舒服”就行，不用精确编码。

## 今日小结

- 日志：`log` 门面 + `env_logger` 实现，用 `info!`/`warn!`/`error!` 打日志，`RUST_LOG=info` 控制级别
- 面向用户的一行错误用 `eprintln!`；程序内部过程记录用 `log`
- **thiserror**：`#[derive(Error)]` + `#[error("...")]` + `#[from]`，给库定义可被 match 的错误枚举
- **anyhow**：`Result<()>` + `.context("...")`，给应用快速包装任意错误，`?` 自动转换
- 分工：库用 thiserror，应用用 anyhow

## 动手任务

1. 给你之前的 minigrep 项目加上 `env_logger`，在“读到文件”“开始搜索”“找到 N 条结果”处打 `info!`，用 `$env:RUST_LOG="info"; cargo run -- 词 文件` 看输出。
2. 用 thiserror 给 minigrep 定义一个 `MiniGrepError`，把参数错误和文件错误分别建模，在 main 里 match 打印。
3. 写一个小程序用 anyhow：读一个不存在的文件并 `.context("读取配置失败")`，运行看错误信息是否带上下文。

> 提示：env_logger 要在 `main` 第一行调 `env_logger::init()`，否则前面的日志会丢。

**配合阅读**：[log crate 文档](https://docs.rs/log) · [env_logger 文档](https://docs.rs/env_logger) · [thiserror 文档](https://docs.rs/thiserror) · [anyhow 文档](https://docs.rs/anyhow)
