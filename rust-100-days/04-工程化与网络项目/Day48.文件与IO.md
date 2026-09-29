# Day48 文件与 IO

> 目标：学会用标准库读写文件、遍历目录、处理命令行参数，写出能和文件系统打交道的命令行程序。

## 标准库里的 IO 全家福

Rust 标准库把输入输出放在 `std::io` 和 `std::fs` 两个模块里。常用的就这几样：

| 模块 | 干嘛的 |
|---|---|
| `std::env::args()` | 读命令行参数 |
| `std::fs::read_to_string(路径)` | 整个文件读成 String |
| `std::fs::write(路径, 内容)` | 把内容整个写进文件 |
| `std::fs::read(路径)` | 整个文件读成 `Vec<u8>`（二进制） |
| `std::fs::read_dir(路径)` | 遍历目录下的条目 |
| `std::fs::create_dir` / `remove_file` | 建目录 / 删文件 |
| `std::io::stdin()` | 从终端逐行读用户输入 |

几乎所有这些函数都返回 `Result<T>`，因为 IO 操作天然可能失败（文件不存在、没权限、磁盘满了）。

## 读命令行参数

`std::env::args()` 返回一个迭代器，把它 `collect` 成 `Vec<String>` 最直观：

```rust
use std::env;

fn main() {
    let args: Vec<String> = env::args().collect();
    // args[0] 是程序自身路径，args[1] 起才是用户传入的参数
    println!("程序名: {}", args[0]);
    if args.len() > 1 {
        println!("第一个参数: {}", args[1]);
    }
}
```

运行下面命令看看输出（注意 `cargo run` 后面跟参数要加 `--` 隔开）：

```powershell
cargo run -- hello world
```

> 注意：`env::args` 在参数含无效 UTF-8 时会 panic。需要处理这种极端情况用 `env::args_os()`，但日常基本用不到。

## 读文件：fs::read_to_string

最简单的一步到位写法——打开文件、读成 String、返回 `Result`：

```rust
use std::fs;

fn main() {
    // 读一个文本文件，失败时 panic（示例用，真实项目要好好处理错误）
    let contents = fs::read_to_string("poem.txt")
        .expect("读 poem.txt 失败");
    println!("文件内容:\n{}", contents);
}
```

生活比喻：`read_to_string` 就像“一口气把整杯水倒进杯子”，适合小文件。大文件要流式读，后面会讲。

按行遍历文件内容（不一次性占满内存）：

```rust
use std::fs::File;
use std::io::{BufRead, BufReader};

fn main() -> std::io::Result<()> {
    let file = File::open("poem.txt")?;
    let reader = BufReader::new(file);   // 带缓冲的读取器，逐行读更快

    for line in reader.lines() {
        let line = line?;                // 每行也是一个 Result
        println!("读到一行: {}", line);
    }
    Ok(())
}
```

关键点：

- `File::open` 打开已有文件（只读）；`File::create` 创建/覆盖文件（只写）
- `BufReader` 套一层缓冲，逐行读时避免每行都做系统调用
- `lines()` 返回的迭代器每个元素是 `io::Result<String>`，要用 `?` 拆开

## 写文件：fs::write

写文件同样有一步到位的写法：

```rust
use std::fs;

fn main() -> std::io::Result<()> {
    fs::write("output.txt", "你好，文件！\n第二行。")?;
    println!("写入完成");
    Ok(())
}
```

`fs::write` 会创建文件（如果不存在）或覆盖已有文件。想追加内容而不是覆盖，要手动用 `OpenOptions`：

```rust
use std::fs::OpenOptions;
use std::io::Write;

fn main() -> std::io::Result<()> {
    let mut file = OpenOptions::new()
        .append(true)      // 追加模式，而不是覆盖
        .create(true)      // 不存在就创建
        .open("output.txt")?;
    writeln!(file, "这是追加的一行")?;
    Ok(())
}
```

> `writeln!` 和 `println!` 用法一样，只是目标是一个文件而不是终端。

## 遍历目录：read_dir

列出某个目录下所有文件：

```rust
use std::fs;

fn main() -> std::io::Result<()> {
    for entry in fs::read_dir(".")? {
        let entry = entry?;                          // 每个条目也是 Result
        let path = entry.path();                      // 拿到完整路径
        if path.is_file() {
            println!("文件: {}", path.display());
        } else if path.is_dir() {
            println!("目录: {}/", path.display());
        }
    }
    Ok(())
}
```

其他常用目录操作：

```rust
fs::create_dir("my_dir")?;          // 建单层目录
fs::create_dir_all("a/b/c")?;       // 递归建多层目录
fs::rename("old.txt", "new.txt")?; // 重命名/移动
fs::remove_file("tmp.txt")?;        // 删文件
```

## 从终端读用户输入

```rust
use std::io;

fn main() {
    let mut input = String::new();
    io::stdin()
        .read_line(&mut input)        // 读一行（含换行符）到 input
        .expect("读取输入失败");
    println!("你输入的是: {}", input.trim());  // trim 去掉首尾换行
}
```

## 错误处理小建议

今天这些函数都返回 `Result`。在小程序里可以用 `expect("说明")` 快速失败，但在真实工具里更好的做法是：让 `main` 返回 `Result`，用 `?` 传播错误，错误信息用 `eprintln!` 打到标准错误（Day53 会完整做一遍这个项目）。

## 今日小结

- `env::args().collect::<Vec<String>>()` 读命令行参数，`args[0]` 是程序名
- `fs::read_to_string(路径)` 一步读文本文件；`fs::write(路径, 内容)` 一步写文件
- 大文件逐行读：`File::open` + `BufReader::new(file).lines()`
- `fs::read_dir(路径)` 遍历目录；`create_dir_all` 递归建目录
- `io::stdin().read_line(&mut s)` 读终端输入；IO 函数都返回 `Result`

## 动手任务

1. 写一个小程序：接收一个文件路径作为参数，读出来并打印行数和字符数。
2. 写一个小程序：在当前目录下创建 `output/` 文件夹，往里写一个 `hello.txt`，内容是当前时间（可以用 `std::time::SystemTime`，不知道怎么用就先写死一行文字）。
3. 用 `fs::read_dir(".")` 列出当前目录下所有 `.rs` 文件的文件名。

> 提示：按后缀过滤可以用 `path.extension()`，比如 `path.extension() == Some(std::ffi::OsStr::new("rs"))`。

**配合阅读**：[官方书·12.1 接受命令行参数](https://rustwiki.org/zh-CN/book/ch12-01-accepting-command-line-arguments.html) · [官方书·12.2 读取文件](https://rustwiki.org/zh-CN/book/ch12-02-reading-a-file.html) · [std::fs 文档](https://doc.rust-lang.org/std/fs/index.html)
