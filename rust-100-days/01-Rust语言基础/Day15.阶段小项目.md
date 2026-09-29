# Day15 阶段小项目

> 目标：把前 14 天的东西串起来，做一个能和你"交互"的命令行小程序——从键盘读入温度，换算成华氏度，并复盘第一阶段。

## 项目介绍

我们做一个**交互式温度换算器**：

1. 程序提示用户："请输入摄氏温度："
2. 用户在键盘敲一个数字（比如 `26`）回车
3. 程序读出这个数，换算成华氏度并打印：`26.0℃ = 78.8℉`

这是你第一次写"能跟人交互"的 Rust 程序。它会用到：变量、函数、`String`、引用、控制流、以及新学的"读取键盘输入"。这个"读输入 → 处理 → 输出"的骨架，正是 Day53 那个经典命令行项目 minigrep 的雏形。

## 新建项目

```powershell
cargo new temp_converter
cd temp_converter
```

把下面的代码整个贴进 `src/main.rs`。先通读一遍，我再逐段解释。

```rust
use std::io;   // 引入标准库里的"输入输出"模块

fn main() {
    println!("=== 摄氏 → 华氏 换算器 ===");

    loop {
        // 1. 提示用户输入
        println!("请输入摄氏温度（输入 q 退出）：");

        // 2. 准备一个空字符串，用来存用户敲进来的内容
        let mut input = String::new();

        // 3. 读取一行键盘输入
        io::stdin()
            .read_line(&mut input)
            .expect("读取输入失败");

        // 4. 去掉首尾空白（含回车换行）
        let input = input.trim();

        // 5. 用户输入 q 就退出循环
        if input == "q" {
            println!("再见！");
            break;
        }

        // 6. 把字符串解析成小数
        let celsius: f64 = match input.parse() {
            Ok(num) => num,
            Err(_) => {
                println!("⚠️  '{}' 不是有效数字，重新输入吧", input);
                continue;          // 跳过本次，回到循环开头
            }
        };

        // 7. 换算并打印
        let fahrenheit = celsius * 9.0 / 5.0 + 32.0;
        println!("结果：{:.1}℃ = {:.1}℉\n", celsius, fahrenheit);
    }
}
```

## 运行

```powershell
cargo run
```

试着输入 `26`、`-5`、`abc`、`q`，看看分别发生什么。

## 逐段讲解（重点在"读输入"）

### `use std::io;`

引入标准库的输入输出模块。没有这行，后面写 `io::stdin()` 找不到。

### 读取一行输入的三行套路

```rust
let mut input = String::new();          // 准备一个可变空字符串
io::stdin()
    .read_line(&mut input)               // 把用户输入"填进"这个字符串
    .expect("读取输入失败");
```

- `read_line` 会**阻塞**等用户敲回车，然后把整行内容（包括结尾的换行符 `\n`）追加到 `input` 里。
- 它要求传一个 `&mut String`（可变引用），所以 `input` 必须是 `mut` 的。
- 返回一个 `Result` 类型，`.expect(...)` 是"万一出错就报错退出"的简单写法（后面 Day25 会系统学错误处理，现在先这么用）。

### 清洗与解析

```rust
let input = input.trim();               // 去掉首尾空格和换行
let celsius: f64 = match input.parse() {
    Ok(num) => num,                     // 解析成功，拿到数字
    Err(_) => { ... continue; }         // 解析失败，提示并 continue
};
```

- `trim()` 把输入尾巴上的回车换行去掉，否则解析容易出问题。
- `parse()` 尝试把字符串转成数字。它可能成功（`Ok`）也可能失败（`Err`）。`match` 是 Rust 处理"两种情况"的方式，今天先会用就行，Day19 会专门学。
- 解析失败时用 `continue` 回到循环开头，让用户重输，程序不会崩。

### 格式化打印

`{:.1}` 表示保留 1 位小数，这样输出干净好看。

## 这个项目用到了哪些知识

| 代码 | 对应哪天学的 |
|---|---|
| `String::new()`、`&mut input` | Day11 引用与借用、Day13 字符串 |
| `let mut input` | Day03 可变性 |
| `loop` / `break` / `continue` | Day09 循环 |
| `if input == "q"` | Day08 控制流 |
| `celsius * 9.0 / 5.0 + 32.0`、`as` 思路 | Day05 运算符 |
| `{:.1}` 占位符 | Day02 `println!` |
| `input.parse()` + `match` | 新东西，错误处理的雏形（Day25 详解） |

## 复盘第一阶段（Day01~15）

恭喜你走完了 Rust 语言基础的第一程！回顾一下：

- **环境与工具链**：rustup、cargo、VS Code（Day01~02）
- **核心语法**：变量与可变性、数据类型、运算符、函数、注释、控制流、循环（Day03~09）
- **Rust 灵魂**：所有权、引用与借用、切片（Day10~12）——这是和别的语言最不一样的地方
- **字符串与实战**：`String`/`&str`、综合练习、交互小程序（Day13~15）

接下来第二阶段（Day16~30），我们会学结构体、枚举、`match`、集合、错误处理、泛型和 trait，最后做一个完整的猜数字游戏。所有权这些"底层规则"你后面会天天用到，不用强求一次全懂，多写代码自然就顺了。

## 今日小结

- 交互程序套路：`String::new()` + `io::stdin().read_line(&mut s)` + `trim()` + `parse()`。
- 用 `loop` 包起交互，用户想退出就 `break`，输入非法就 `continue` 重输。
- `parse()` 返回 `Result`，用 `match` 分别处理成功和失败，这是以后错误处理的基础。
- "读输入 → 处理 → 输出"是所有命令行工具的骨架，minigrep 也是这个结构。

## 动手任务

1. 把程序改成**华氏 → 摄氏**（公式：`(华氏 - 32) * 5 / 9`）。
2. 增加一个功能：换算完后，判断温度 `celsius`，打印一句评价（如 `<0` "冰冻"、`<30` "舒适"、否则 "很热"）。
3. （挑战）用命令行参数而不是交互输入：让程序读 `std::env::args()` 的第一个数字参数直接换算，为 Day48 的"命令行参数"做个预告。

**配合阅读**：[官方书·第2章 猜数字游戏（交互式程序的完整范例）](https://rustwiki.org/zh-CN/book/ch02-00-guessing-game-tutorial.html)
