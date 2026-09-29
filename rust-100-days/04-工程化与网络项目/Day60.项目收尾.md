# Day60 项目收尾

> 目标：把昨天的项目打磨成一个像模像样的开源作品——补错误处理、补测试、写 README、打包，并做一次复盘。

## 收尾清单

一个“能跑”的项目和一个“像样”的项目，差的就是下面这些。按顺序过一遍：

### 1. 错误处理

- [ ] main 返回 `Result`，用 `?` 传播错误，不再到处 `unwrap`/`expect`（除了示例代码）
- [ ] 库错误用 thiserror 定义了枚举，调用方能 match 不同错误
- [ ] 面向用户的错误信息用 `eprintln!`，不把 panic 堆栈甩给用户
- [ ] 错误信息是人话：`读取 config.txt 失败: No such file or directory` 而不是 `called unwrap on None`

```rust
// 反面教材
fn main() {
    let args: Vec<String> = env::args().collect();
    let filename = &args[1];              // 参数不够直接 panic
    let text = fs::read_to_string(filename).unwrap();  // 文件不存在直接 panic
}
```

```rust
// 正面教材
fn main() -> anyhow::Result<()> {
    let args: Vec<String> = env::args().collect();
    if args.len() < 2 {
        eprintln!("用法: mywc <文件路径>");
        std::process::exit(1);
    }
    let text = fs::read_to_string(&args[1])
        .with_context(|| format!("读取 {} 失败", args[1]))?;
    // ...
    Ok(())
}
```

### 2. 测试

- [ ] 核心纯函数有单元测试（`cargo test` 全绿）
- [ ] 关键路径有集成测试（`tests/` 目录）
- [ ] 文档示例代码块能通过 doc test
- [ ] 跑过 `cargo clippy`，没有未处理的警告

```powershell
cargo test
cargo clippy
cargo fmt -- --check
```

### 3. 日志

- [ ] 关键步骤有 `info!`（启动完成、处理了 N 个文件）
- [ ] 异常但可恢复的情况用 `warn!`
- [ ] `env_logger::init()` 在 main 第一行

### 4. README

项目根目录写一个 `README.md`，至少包含：

- **项目是什么**：一句话
- **长什么样 / 截图**（CLI 工具就贴一段终端输出）
- **怎么安装**：`cargo install 包名` 或 `cargo build --release`
- **怎么用**：带例子
- **用了哪些依赖**：简单提一下

一个最小 README 模板：

```markdown
# mywc

一个统计文件行数/词数/字符数的命令行工具。

## 安装

    cargo install mywc

## 使用

    mywc poem.txt
    # => 行数: 9  词数: 55  字符数: 320

## 开发

    cargo test
```

### 5. 打包发布

```powershell
cargo build --release
```

产物在 `target/release/` 下（Windows 是 `.exe`）。你可以把它拷给别人直接跑。

如果想让别人 `cargo install` 装你这个工具，按 Day47 的流程：填好 `Cargo.toml` 的 `description`/`license`，`cargo publish`。不发布也没关系——本地 release 产物已经能用了。

### 6. 清理

- [ ] 删掉调试用的 `println!`（该用 `log` 的换 `log`）
- [ ] 删掉 `TODO` / `unimplemented!` 占位
- [ ] `.gitignore` 里有 `target/`（cargo new 自动生成了）

## 复盘模板

项目做完了，花 15 分钟填下面这张表——这比多写 100 行代码更值：

```markdown
## 我的项目：__________

### 我做了什么
（一句话描述功能）

### 我用上了哪些前 60 天的知识
- [ ] 所有权 / 借用
- [ ] Result / 错误处理
- [ ] Vec / HashMap
- [ ] 迭代器
- [ ] 模块系统（lib.rs / main.rs 分离）
- [ ] 测试（单元 / 集成 / 文档）
- [ ] 文件 IO
- [ ] serde（如果用了）
- [ ] 网络 / HTTP（如果做了 Web）
- [ ] 多线程（如果做了服务器）
- [ ] clippy / cargo fmt

### 卡住最久的地方
（记录报错和怎么解决的，下次就不会再卡）

### 我学到的最重要的一件事
（一句话）

### 下一步想加什么功能
（哪怕只是想法）
```

## 前 60 天回顾

到今天为止，你已经走完了“Rust 语言基础 → 核心机制 → 进阶特性 → 工程化与网络项目”四个阶段。回头看：

- Day01~15：语法、变量、函数、控制流
- Day16~30：所有权、生命周期、结构体、枚举、错误处理、测试
- Day31~45：闭包、迭代器、智能指针、多线程、异步、unsafe、宏
- Day46~60：Cargo、文档、文件 IO、serde、网络、Web 服务器、工程化错误与测试

接下来 Day61 起就要进入**系统编程与操作系统**方向（no_std、裸机、页表、rCore）——那才是开源操作系统训练营的主战场。今天这个项目就是你走进系统编程前的最后一次“普通 Rust”练兵。

## 今日小结

- 收尾清单：错误处理（anyhow/thiserror）、测试（cargo test/clippy/fmt）、日志、README、release 打包
- `cargo build --release` 出可执行文件；想分发就按 Day47 流程 `cargo publish`
- 项目做完一定要复盘：记录卡点、学到的东西、下一步想法
- 前 60 天工程化阶段结束，准备进入系统编程

## 动手任务

1. 按上面的收尾清单逐项检查你的项目，每一项打勾或修复。
2. 写好 `README.md`，贴一段真实运行截图/终端输出。
3. 运行 `cargo build --release`，把 `target/release/` 下的可执行文件拷出来，在一个新终端里直接运行（不通过 cargo），确认它能独立工作。
4. 填一遍复盘模板。

> 提示：Windows 下 release 产物在 `target\release\mywc.exe`（假设项目叫 mywc）。

**配合阅读**：[官方书·第14章 Cargo 更多知识](https://rustwiki.org/zh-CN/book/ch14-00-more-about-cargo.html) · [官方书·第12章 I/O 项目](https://rustwiki.org/zh-CN/book/ch12-00-an-io-project.html)
