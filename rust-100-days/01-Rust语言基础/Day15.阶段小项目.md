# Day15 阶段小项目

> **前置知识**：Day01~Day14（变量、函数、`String`、引用、控制流、循环）
> **难度**：入门
> **预计阅读时间**：25 分钟
> **代码说明**：本章代码都放在同一个项目的 `src/main.rs` 里。下面三个 `rust` 块都是能单独跑通的完整程序：第一块是项目本体，后两块是把其中两个关键步骤拆出来单独给你看的小程序。换块时把 `src/main.rs` 整个替换掉。

---

## 项目介绍

做一个**交互式温度换算器**：

1. 程序提示用户："请输入摄氏温度："
2. 用户在键盘敲一个数字（比如 `26`）回车
3. 程序读出这个数，换算成华氏度并打印：`26.0℃ = 78.8℉`

这是你第一次写能跟人交互的 Rust 程序。用到变量、函数、`String`、引用、控制流，外加新学的"读取键盘输入"。这套"读输入 → 处理 → 输出"的骨架，就是 Day53 那个经典命令行项目 minigrep 的雏形。

换算公式 Day05 练过：`华氏 = 摄氏 * 9.0 / 5.0 + 32.0`。

## 新建项目

```powershell
cargo new temp_converter
cd temp_converter
```

把下面整个程序贴进 `src/main.rs`。

📦 本块能单独跑（项目本体）：

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

## 逐段讲解

### `use std::io;`

标准库把输入输出放在 `std::io` 这个模块里。`use` 把这个名字引进当前文件，没有这行，后面写 `io::stdin()` 编译器就找不到 `io`。（模块和路径的完整规则 Day21 讲。）

### 读取一行输入

先把这几行单独拎出来跑一遍，比夹在整段代码里更容易看清：

📦 本块能单独跑：

```rust
use std::io;

fn main() {
    println!("随便打几个字，然后回车：");

    let mut input = String::new();          // 准备一个可变空字符串
    io::stdin()
        .read_line(&mut input)              // 把用户输入"填进"这个字符串
        .expect("读取输入失败");

    println!("你输入的是：{}", input.trim());  // trim 剪掉尾巴上的换行
}
```

- `String::new()` 造一个空字符串（Day13）。
- `read_line` 会**阻塞**程序，等用户敲回车，然后把整行内容填进 `input`。注意它是往后面**追加**，不是清空再写。
- 它要求传一个 `&mut String`（可变引用，Day11），所以 `input` 必须声明成 `mut`。
- `read_line` 返回一个 `Result` 类型，表示"这次操作要么成功、要么失败"。`Result` 有 `Ok` 和 `Err` 两种情况，Day19 学 `match`、Day25 学错误处理时会正式讲。
- `.expect("读取输入失败")` 是最省事的处理方式：一旦失败就立刻报错退出，并打印你写的那句话。现在先这么用。
- 读进来的内容**带着结尾的换行符 `\n`**，所以最后要 `trim()` 剪掉它。

### 清洗与解析

📦 本块能单独跑：

```rust
use std::io;

fn main() {
    let mut input = String::new();
    io::stdin().read_line(&mut input).expect("读取输入失败");

    let input = input.trim();               // 去掉首尾空格和换行
    let celsius: f64 = match input.parse() {
        Ok(num) => num,                     // 解析成功，拿到数字
        Err(_) => {
            println!("'{}' 不是有效数字", input);
            return;                         // 独立小程序里直接退出
        }
    };

    println!("解析结果：{} 摄氏度", celsius);
}
```

- `let input = input.trim();` 这行用了**变量遮蔽**（Day03 讲过）：右边拿旧的 `input` 算出一个 `&str`，左边又声明一个叫 `input` 的新变量把旧的遮住。带换行的旧字符串之后就拿不到了。
- `input.parse()` 尝试把字符串转成数字。转成哪种数字，由左边的类型标注 `let celsius: f64` 说了算（`f64` 是双精度浮点，Day04 讲过）。
- 解析可能成功也可能失败，所以用 `match` 把两种情况分开写。`match` 是 Rust 处理多种情况的主要工具，Day19 专门学，今天先会用。
- `Err(_)` 里的 `_` 表示"这个失败原因我不用，别起名字"（Day14 见过这个写法）。
- 真正的项目里，解析失败时用的是 `continue`，回到循环开头让用户重输；上面这个独立小程序没有循环可回，所以用 `return` 直接结束。

### 格式化打印

`{:.1}` 表示这个位置放一个浮点数，并保留 1 位小数。`{}` 占位符 Day02 讲过，`.1` 是本章新加的精度写法，加上它输出会整齐很多。

## 这个项目用到了哪些知识

| 代码 | 对应哪天学的 |
|---|---|
| `String::new()`、`&mut input` | Day11 引用与借用、Day13 字符串 |
| `let mut input` | Day03 可变性 |
| `let input = input.trim()` | Day03 变量遮蔽 |
| `loop` / `break` / `continue` | Day09 循环 |
| `if input == "q"` | Day08 控制流 |
| `celsius * 9.0 / 5.0 + 32.0` | Day05 运算符（数字都带 `.0`，避开整数除法） |
| `{}` 占位符 | Day02 `println!` |
| `{:.1}` 精度写法 | 本章新学 |
| `input.parse()` + `match` | 新东西，错误处理的雏形（Day19 学 `match`，Day25 详解错误处理） |

## 复盘第一阶段（Day01~15）

Rust 语言基础走到这里就结束了。回顾一遍：

- **环境与工具链**：rustup、cargo、VS Code（Day01~02）
- **核心语法**：变量与可变性、数据类型、运算符、函数、注释、控制流、循环（Day03~09）
- **Rust 灵魂**：所有权、引用与借用、切片（Day10~12）。这三天的内容和别的语言差别最大
- **字符串与实战**：`String`/`&str`、综合练习、交互小程序（Day13~15）

接下来第二阶段（Day16~30）学结构体、枚举、`match`、集合、错误处理、泛型和 trait，最后做一个完整的猜数字游戏。所有权这些底层规则后面天天会用，不用强求一次全懂，多写代码自然就顺了。

## 今日小结

- 交互程序套路：`String::new()` + `io::stdin().read_line(&mut s)` + `trim()` + `parse()`。
- 用 `loop` 包住交互，用户想退出就 `break`，输入非法就 `continue` 重输。
- `parse()` 返回 `Result`，用 `match` 分别处理成功和失败，这是以后错误处理的基础。
- "读输入 → 处理 → 输出"是所有命令行工具的骨架，minigrep 也是这个结构。

## 动手任务

1. 把程序改成**华氏 → 摄氏**（公式：`(华氏 - 32) * 5 / 9`）。
2. 增加一个功能：换算完后判断温度 `celsius`，打印一句评价（小于 0 打"冰冻"、小于 30 打"舒适"、其余打"很热"）。
3. （挑战）不用交互输入，改成读命令行参数：让程序拿 `std::env::args()` 里第一个数字参数直接换算。命令行参数 Day48 会正式讲。

**配合阅读**：[官方书·第2章 猜数字游戏（交互式程序的完整范例）](https://rustwiki.org/zh-CN/book/ch02-00-guessing-game-tutorial.html)
