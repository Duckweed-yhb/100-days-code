# Day85 命令行工具生态

> 目标：用 clap 写出参数解析漂亮的 CLI，认识 anyhow / indicatif / ratatui 这套「Rust 写命令行」的标配，并知道哪些明星工具其实是 Rust 写的。

## 为什么 Rust 是写 CLI 的天选语言

命令行工具的诉求很明确：**单文件分发、启动快、不依赖运行时**。Python 要装解释器，Go 不错但 Rust 的二进制更小、错误处理更严。所以这几年一批你天天用的工具都换成了 Rust。

### 明星工具（它们都是 Rust 写的）

| 工具 | 替代了什么 | 干嘛的 |
|---|---|---|
| ripgrep (`rg`) | grep | 超快的代码搜索，默认递归、自动忽略 .gitignore |
| fd | find | 更友好的文件查找 |
| bat | cat | 带语法高亮的文件查看 |
| eza | ls | 带图标和颜色的目录列表 |
| zellij / tmux-rs | tmux | 终端复用器 |
| ripgrep / fd / bat / eza | —— | 这几个装上去，终端体验直接升级 |

## 参数解析：clap

clap 是 Rust 命令行参数解析的事实标准。现在推荐 **derive 模式**：用结构体描述参数，clap 自动生成 `--help`、错误提示、补全。

```toml
[dependencies]
clap = { version = "4", features = ["derive"] }
anyhow = "1"
indicatif = "0.17"
```

```rust
use clap::Parser;

/// 一个把文件里单词统计出来的小工具
#[derive(Parser, Debug)]
#[command(version, about = "统计文件里的单词数")]
struct Args {
    /// 要统计的文件路径
    file: String,

    /// 统计时是否忽略大小写
    #[arg(short, long)]
    ignore_case: bool,

    /// 最少出现多少次才显示
    #[arg(short, long, default_value_t = 1)]
    min_count: u32,
}

fn main() -> anyhow::Result<()> {
    let args = Args::parse();  // 一行解析全部参数，--help 自动生成
    println!("文件：{}，忽略大小写：{}", args.file, args.ignore_case);
    // ... 真正的统计逻辑
    Ok(())
}
```

跑 `cargo run -- --help`，你会看到自动生成的帮助信息——注释 `///` 直接变成帮助文本。

## 错误处理：anyhow

CLI 的 `main` 想直接返回错误？标准库的 `Box<dyn Error>` 太丑。**anyhow** 提供一个万能 `Result`：

```rust
fn main() -> anyhow::Result<()> {
    let content = std::fs::read_to_string("不存在.txt")?;  // 错误自动包装
    Ok(())
}
```

它适合「程序边界」——你不打算设计精细错误类型时，用它最省事。（库内部还是要定义自己的错误类型，见 Day56。）

## 进度条：indicatif

跑一个长任务（批量处理文件），没进度条用户以为卡死了。indicatif 几行搞定：

```rust
use indicatif::{ProgressBar, ProgressStyle};
use std::thread::sleep;
use std::time::Duration;

let pb = ProgressBar::new(100);  // 总共 100 步
pb.set_style(
    ProgressStyle::default_bar()
        .template("[{bar:40.cyan/blue}] {pos}/{len} ({eta})")?
        .progress_chars("#>-"),
);

for _ in 0..100 {
    sleep(Duration::from_millis(20));
    pb.inc(1);  // 走一步
}
pb.finish_with_message("完成！");
```

## 终端 UI：ratatui

如果你想做更复杂的全屏界面（比如 htop 那种仪表盘），用 **ratatui**（终端 UI 库）。它能在终端里画表格、图表、进度条。入门门槛比前面几个高，先知道有这东西，需要时再查。

## 今日小结

- Rust 写 CLI：单文件、启动快、无运行时依赖
- clap derive 模式：结构体描述参数，自动生成 --help
- anyhow 给 main 一个省事的万能 Result；indicatif 画进度条
- ratatui 做全屏终端 UI（htop 风格）
- ripgrep/fd/bat/eza 都是 Rust 写的，可直接装来用

## 动手任务

1. 用 clap 写一个接受文件路径 + `--ignore-case` 参数的小工具
2. 给循环任务加一个 indicatif 进度条
3. 装一个 `rg`（ripgrep），用它在你某个项目里搜一个函数名，感受和 grep 的差别

**配合阅读**：[clap 文档](https://docs.rs/clap/latest/clap/)
