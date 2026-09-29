# Day30 阶段项目：猜数字游戏

> 目标：把前 29 天学的变量、引用、字符串、枚举、match、循环、错误处理、外部 crate 全串起来，从零手敲一个能玩的猜数字游戏。

## 游戏规则

这是《Rust 程序设计语言》第 2 章的经典项目，我们一步步来：

1. 程序**随机生成**一个 1~100 的秘密数字；
2. 提示玩家输入一个猜测；
3. 告诉玩家猜大了还是小了；
4. 猜对了，打印祝贺并退出。

别直接复制粘贴——跟着敲一遍，你会把前面学的东西真正串起来。

## 第一步：创建项目

```powershell
cargo new guessing_game
cd guessing_game
```

打开 `src/main.rs`，把它清空，我们从头写。

## 第二步：先让程序能读输入

先实现「打印提示 → 读一行输入 → 回显」：

```rust
use std::io; // 引入标准库的输入输出模块

fn main() {
    println!("猜数字游戏！");
    println!("请输入你猜的数字：");

    // 可变空字符串，用来存用户输入
    let mut guess = String::new();

    // 从标准输入读一行，追加到 guess；出错就 panic
    io::stdin()
        .read_line(&mut guess)
        .expect("读取输入失败");

    println!("你猜的是：{}", guess);
}
```

逐行解释几个关键点：

- `let mut guess = String::new();`：`String::new()` 是关联函数（Day16），造一个空的可变字符串。
- `io::stdin().read_line(&mut guess)`：`read_line` 需要**可变引用**才能往 `guess` 里写内容，所以是 `&mut guess`（Day11）。它返回一个 `Result`。
- `.expect("读取输入失败")`：`Result` 是 `Ok` 就取出里面的值（读到的字节数），是 `Err` 就 panic（Day25）。不写它编译会警告「未处理 Result」。

跑一下：

```powershell
cargo run
```

输入一个数字回车，程序会把你输入的内容原样打印出来。

## 第三步：引入 rand，生成秘密数字

Rust 标准库不带随机数，得用外部 crate `rand`。打开 `Cargo.toml`，在 `[dependencies]` 下加一行：

```toml
[dependencies]
rand = "0.8.5"
```

保存后跑一次 `cargo build`，Cargo 会自动从 crates.io 把 rand 及其依赖下载并编译下来。第一次会慢一点，之后就有缓存了。

现在改 `src/main.rs`：

```rust
use std::io;
use rand::Rng; // Rng 是 trait，要 use 进来才能用 gen_range 方法

fn main() {
    println!("猜数字游戏！");

    // thread_rng() 拿到当前线程的随机数生成器
    // gen_range(1..=100) 在 [1, 100] 闭区间里取随机整数
    let secret_number = rand::thread_rng().gen_range(1..=100);

    // 开发期间先打印秘密数字，方便调试；最后删掉
    println!("秘密数字是：{}", secret_number);

    println!("请输入你猜的数字：");

    let mut guess = String::new();
    io::stdin()
        .read_line(&mut guess)
        .expect("读取输入失败");

    println!("你猜的是：{}", guess);
}
```

跑 `cargo run`，每次都会打印一个不同的秘密数字（1~100）。

> 小知识：`1..=100` 是闭区间（含两端）。如果你写 `1..101` 是左闭右开，效果一样。

## 第四步：比较猜测和秘密数字

我们要把玩家输入和秘密数字比大小。先加一个 import：

```rust
use std::cmp::Ordering;
```

`Ordering` 是个枚举，有三个变体：`Less`、`Greater`、`Equal`——正好对应「小了 / 大了 / 相等」。在回显之后加：

```rust
    match guess.cmp(&secret_number) {
        Ordering::Less => println!("太小了！"),
        Ordering::Greater => println!("太大了！"),
        Ordering::Equal => println!("你赢了！"),
    }
```

但现在直接 `cargo build` 会报错——因为 `guess` 是 `String`，`secret_number` 是数字，Rust 不让直接比。我们得把字符串转成数字。把原来那行 `let mut guess = String::new();` 之后的逻辑改成：

```rust
    let mut guess = String::new();
    io::stdin()
        .read_line(&mut guess)
        .expect("读取输入失败");

    // 把字符串解析成 u32；用 let guess 遮蔽掉上面的字符串版 guess
    let guess: u32 = guess.trim().parse().expect("请输入一个数字！");

    println!("你猜的是：{}", guess);

    match guess.cmp(&secret_number) {
        Ordering::Less => println!("太小了！"),
        Ordering::Greater => println!("太大了！"),
        Ordering::Equal => println!("你赢了！"),
    }
```

逐句说：

- `guess.trim()`：去掉输入首尾的空白和换行符（read_line 会把回车也读进来）。
- `.parse()`：把字符串解析成数字，返回 `Result`。
- `let guess: u32`：**遮蔽**了之前那个字符串 `guess`，告诉 `parse` 我要的是 `u32`。
- `.expect("请输入一个数字！")`：`parse` 失败（输入不是数字）就 panic 并打印这条消息。注意 `expect` 是方法，后面跟括号，不是宏（不要写成 `.expect!(...)`）。

## 第五步：加循环，让玩家能一直猜

现在程序猜一次就退出了。用 `loop` 包起来，让玩家反复猜：

```rust
use std::cmp::Ordering;
use std::io;
use rand::Rng;

fn main() {
    println!("猜数字游戏！");

    let secret_number = rand::thread_rng().gen_range(1..=100);
    println!("秘密数字是：{}", secret_number); // 调试用，最后删

    loop {
        println!("请输入你猜的数字：");

        let mut guess = String::new();
        io::stdin()
            .read_line(&mut guess)
            .expect("读取输入失败");

        let guess: u32 = match guess.trim().parse() {
            Ok(num) => num,
            Err(_) => {
                println!("请输入一个有效的数字！");
                continue; // 输入非法，跳过本次，继续下一轮
            }
        };

        println!("你猜的是：{}", guess);

        match guess.cmp(&secret_number) {
            Ordering::Less => println!("太小了！"),
            Ordering::Greater => println!("太大了！"),
            Ordering::Equal => {
                println!("你赢了！");
                break; // 猜对了，跳出循环
            }
        }
    }
}
```

两个关键改动：

1. **非法输入不再崩溃**：把 `parse().expect(...)` 换成 `match`——解析成功就拿数字，失败就提示一句 `continue` 继续下一轮（这正是 Day19/Day25 学的）。
2. **猜对就 `break`**：`Ordering::Equal` 分支里 `break`，跳出 loop，程序结束。

## 第六步：收尾

- 把那行 `println!("秘密数字是：{}", secret_number);` 删掉——不然开局就剧透，游戏没法玩了。
- 跑 `cargo run`，开始玩吧。

最终完整代码长这样（删掉调试那行后）：

```rust
use std::cmp::Ordering;
use std::io;
use rand::Rng;

fn main() {
    println!("猜数字游戏！");

    let secret_number = rand::thread_rng().gen_range(1..=100);

    loop {
        println!("请输入你猜的数字：");

        let mut guess = String::new();
        io::stdin()
            .read_line(&mut guess)
            .expect("读取输入失败");

        let guess: u32 = match guess.trim().parse() {
            Ok(num) => num,
            Err(_) => {
                println!("请输入一个有效的数字！");
                continue;
            }
        };

        println!("你猜的是：{}", guess);

        match guess.cmp(&secret_number) {
            Ordering::Less => println!("太小了！"),
            Ordering::Greater => println!("太大了！"),
            Ordering::Equal => {
                println!("你赢了！");
                break;
            }
        }
    }
}
```

## 这个项目用到了哪些知识

| 代码片段 | 来自 |
|---|---|
| `let mut` / `String::new()` | Day03 变量、Day23 字符串 |
| `&mut guess` / `&secret_number` | Day11 引用与借用 |
| `use rand::Rng` / `Cargo.toml` | Day21 模块、Cargo 依赖 |
| `gen_range(1..=100)` | 外部 crate 用法 |
| `guess.trim().parse()` | Day23 字符串方法 |
| `let guess: u32` 遮蔽 | Day03 变量遮蔽 |
| `match ... { Ok/Err }` | Day19 match |
| `Ordering::Less/Greater/Equal` | Day17 枚举 |
| `loop` + `break` / `continue` | Day09 循环 |
| `.expect(...)` / `Err(_) => continue` | Day25 错误处理 |

恭喜你，至此第二阶段「Rust 核心机制」全部学完。你已经能用 Rust 写出一个完整的、带错误处理的命令行小程序了。下一阶段我们会进入闭包、迭代器、智能指针这些进阶特性。

## 今日小结

- 完整跑通：`cargo new` → 读输入 → rand 生成随机数 → 类型转换 → match 比较 → loop 循环
- 字符串读进来是 `String`，要 `.trim().parse::<u32>()` 转成数字才能比
- 用 `match parse` 优雅处理非法输入（`continue`），而不是一报错就崩溃
- 猜对用 `break` 退出循环；调试时打印秘密数字，发布前删掉

## 动手任务

1. 按上面六步亲手敲一遍，不要复制粘贴，跑通游戏。
2. 加一个「猜测次数计数器」：每猜一次 +1，赢了之后打印「你一共猜了 N 次」。提示：在 loop 外 `let mut count = 0;`，循环里 `count += 1;`。
3. （挑战）猜对后问玩家「再玩一局？(y/n)」，输入 `y` 就重新生成秘密数字继续，输入 `n` 才退出。提示：把整个游戏逻辑再套一层外层 `loop`。

> 提示：计数器要在外层声明 `let mut count = 0;`，放在 `loop` 里面的话每轮都会重置成 0。

**配合阅读**：[官方书·第2章 猜数字游戏教程](https://rustwiki.org/zh-CN/book/ch02-00-guessing-game-tutorial.html)
