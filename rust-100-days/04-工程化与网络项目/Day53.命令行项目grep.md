# Day53 命令行项目 grep

> 目标：完整实现一个简化版 `grep` 工具（minigrep），把命令行参数、文件读取、重构、错误处理、测试、环境变量、stderr 输出串成一个真实项目。

## 这个项目要做什么

`grep` 是 Linux 下最常用的命令行工具之一：给它一个搜索词和一个文件，它把文件里包含这个词的行打印出来。我们要做的迷你版：

```powershell
cargo run -- 搜索词 poem.txt
```

它应该打印 `poem.txt` 里所有包含“搜索词”的行。再加一个能力：设置环境变量 `IGNORE_CASE=1` 后，搜索大小写不敏感。

这个项目是官方书第 12 章的完整案例，重点不在功能多复杂，而在**怎么把一个脚本式的 main 函数，重构成一个可测试、错误处理友好的工程化项目**。

## 第一步：从零开始，先跑通最小版本

新建项目：

```powershell
cargo new minigrep
cd minigrep
```

先写最朴素的版本——把参数和文件内容直接打印出来：

```rust
// src/main.rs
use std::env;
use std::fs;

fn main() {
    let args: Vec<String> = env::args().collect();

    // args[0] 是程序名，args[1] 是搜索词，args[2] 是文件名
    let query = &args[1];
    let filename = &args[2];

    println!("搜索: {}", query);
    println!("文件: {}", filename);

    let contents = fs::read_to_string(filename)
        .expect("读取文件失败");

    println!("\n文件内容:\n{}", contents);
}
```

准备一个测试文件 `poem.txt`：

```text
I'm nobody! Who are you?
Are you nobody, too?
Then there's a pair of us - don't tell!
They'd banish us, you know.

How dreary to be somebody!
How public, like a frog
To tell your name the livelong day
To an admiring bog!
```

运行：

```powershell
cargo run -- to poem.txt
```

能打印出搜索词和文件内容，第一步通了。

## 第二步：写搜索逻辑

把“找包含搜索词的行”这件事做出来：

```rust
// src/main.rs（在 main 里）
for line in contents.lines() {
    if line.contains(query) {
        println!("{}", line);
    }
}
```

再跑 `cargo run -- to poem.txt`，你应该看到所有含 "to" 的行。

## 第三步：重构——把逻辑挪进 lib.rs

现在 main.rs 里混着参数解析、文件读取、搜索逻辑，又乱又不好测试。Rust 社区的惯例是**关注分离**：

- `main.rs` 只当薄壳：解析参数 → 调库 → 出错时打印退出
- 真正的逻辑放 `src/lib.rs`，可以 `#[test]` 直接测

### 3.1 把配置封装成 Config 结构体

```rust
// src/lib.rs
pub struct Config {
    pub query: String,
    pub filename: String,
}

impl Config {
    pub fn new(args: &[String]) -> Result<Config, &'static str> {
        // 参数不够时返回 Err，而不是 panic
        if args.len() < 3 {
            return Err("用法: minigrep 搜索词 文件路径");
        }
        let query = args[1].clone();
        let filename = args[2].clone();
        Ok(Config { query, filename })
    }
}
```

> 为什么 `clone()`？因为 `args` 是 main 里的 `Vec<String>`，我们借了它的 slice `&[String]`，但 Config 需要拥有自己的 String（活过这个函数）。后面 Day32 学了迭代器可以去掉 clone，今天先用 clone 把逻辑跑通。

### 3.2 把搜索和运行逻辑也挪进来

```rust
// src/lib.rs 顶部
use std::error::Error;
use std::fs;

pub fn run(config: Config) -> Result<(), Box<dyn Error>> {
    let contents = fs::read_to_string(&config.filename)?;

    for line in search(&config.query, &contents) {
        println!("{}", line);
    }

    Ok(())
}

/// 在 contents 中找包含 query 的行，返回这些行的列表
pub fn search<'a>(query: &str, contents: &'a str) -> Vec<&'a str> {
    contents
        .lines()
        .filter(|line| line.contains(query))
        .collect()
}
```

注意几个点：

- `Box<dyn Error>` 表示“任意错误类型”，`?` 能把 `io::Error` 等自动转成它
- 生命周期 `'a`：返回的 `Vec<&str>` 里的字符串切片借自 `contents`，所以要标注它俩同寿
- `search` 是 `pub` 的纯函数，没有副作用——**这就意味着它可以被单元测试直接调用**

### 3.3 main.rs 变成薄壳

```rust
// src/main.rs
use minigrep::Config;
use std::env;
use std::process;

fn main() {
    let args: Vec<String> = env::args().collect();

    // 解析参数失败：打印用法，退出码 1
    let config = Config::new(&args).unwrap_or_else(|err| {
        eprintln!("参数错误: {}", err);   // 错误走 stderr
        process::exit(1);
    });

    // 运行失败：打印错误，退出码 1
    if let Err(e) = minigrep::run(config) {
        eprintln!("程序错误: {}", e);
        process::exit(1);
    }
}
```

## 第四步：写测试（TDD 思路）

逻辑挪到 lib.rs 后，立刻给 `search` 写测试：

```rust
// src/lib.rs 底部
#[cfg(test)]
mod tests {
    use super::*;

    #[test]
    fn one_result() {
        let query = "duct";
        let contents = "\
Rust:
safe, fast, productive.
Pick three.";

        assert_eq!(
            vec!["safe, fast, productive."],
            search(query, contents)
        );
    }
}
```

运行测试：

```powershell
cargo test
```

`duct` 只出现在第二行，所以断言返回 `vec!["safe, fast, productive."]`。绿了说明搜索逻辑对。

## 第五步：加环境变量控制大小写不敏感

真实 grep 有个 `-i` 参数做大小写不敏感搜索。我们用环境变量 `IGNORE_CASE` 控制：

```rust
// src/lib.rs
pub struct Config {
    pub query: String,
    pub filename: String,
    pub case_insensitive: bool,   // 新增字段
}

impl Config {
    pub fn new(args: &[String]) -> Result<Config, &'static str> {
        if args.len() < 3 {
            return Err("用法: minigrep 搜索词 文件路径");
        }
        let query = args[1].clone();
        let filename = args[2].clone();
        // 设置了 IGNORE_CASE 环境变量就为 true
        let case_insensitive = std::env::var("IGNORE_CASE").is_ok();
        Ok(Config { query, filename, case_insensitive })
    }
}
```

再写一个大小写不敏感的搜索函数：

```rust
pub fn search_case_insensitive<'a>(query: &str, contents: &'a str) -> Vec<&'a str> {
    let query = query.to_lowercase();   // 搜索词先转小写
    contents
        .lines()
        .filter(|line| line.to_lowercase().contains(&query))  // 行也转小写再比
        .collect()
}
```

在 `run` 里根据配置选哪个函数：

```rust
pub fn run(config: Config) -> Result<(), Box<dyn Error>> {
    let contents = fs::read_to_string(&config.filename)?;

    let results = if config.case_insensitive {
        search_case_insensitive(&config.query, &contents)
    } else {
        search(&config.query, &contents)
    };

    for line in results {
        println!("{}", line);
    }
    Ok(())
}
```

给新函数加测试：

```rust
#[test]
fn case_insensitive() {
    let query = "rUsT";
    let contents = "\
Rust:
safe, fast, productive.
Pick three.
Trust me.";

    assert_eq!(
        vec!["Rust:", "Trust me."],
        search_case_insensitive(query, contents)
    );
}
```

运行测试，两个都该过。然后试试环境变量：

```powershell
# PowerShell 里临时设置环境变量再跑
$env:IGNORE_CASE="1"; cargo run -- to poem.txt
# 跑完清掉
Remove-Item Env:\IGNORE_CASE
```

大小写不敏感模式下，"To"、"to"、"TO" 都会被匹配。

## 第六步：为什么错误要用 eprintln!

注意 main.rs 里错误信息用的是 `eprintln!` 而不是 `println!`。这是个重要惯例：

- `println!` 输出到**标准输出（stdout）**——这是程序的正常结果
- `eprintln!` 输出到**标准错误（stderr）**——这是错误信息

这样用户可以把正常结果重定向到文件，错误信息仍然显示在屏幕上：

```powershell
cargo run -- to poem.txt > results.txt
```

如果错误也用 `println!`，重定向后错误就被混进 `results.txt` 了，分不清哪个是结果哪个是错。

## 最终项目结构

```
minigrep/
├── Cargo.toml
├── poem.txt
└── src/
    ├── main.rs    # 薄壳：解析参数 + 调 run + 错误退出
    └── lib.rs     # Config、run、search、search_case_insensitive、测试
```

## 今日小结

- 工程化 CLI 的套路：逻辑放 `lib.rs`，`main.rs` 只做参数解析和错误退出
- `Config::new` 返回 `Result`，参数不对时友好报错而不是 panic
- `run` 返回 `Result<(), Box<dyn Error>>`，内部用 `?` 传播错误
- 纯函数 `search` 方便写单元测试；环境变量 `IGNORE_CASE` 控制行为
- 错误信息走 `eprintln!`（stderr），正常结果走 `println!`（stdout）

## 动手任务

1. 完整敲一遍 minigrep，确保 `cargo test` 两个测试都绿，`cargo run -- to poem.txt` 能正确输出。
2. 加一个 `CASE_SENSITIVE` 反向开关（或者改成读 `IGNORE_CASE` 的值而不是只看有没有设置）。
3. 让 minigrep 支持多个文件参数（`minigrep 词 file1 file2 ...`），在每行结果前打印文件名。

> 提示：多文件时把 `filename: String` 改成 `filenames: Vec<String>`，循环里每个文件调一次 `run` 逻辑。

**配合阅读**：[官方书·第12章 一个 I/O 项目：构建 minigrep](https://rustwiki.org/zh-CN/book/ch12-00-an-io-project.html) · [官方书·13.3 改进 I/O 项目](https://rustwiki.org/zh-CN/book/ch13-03-improving-our-io-project.html)
