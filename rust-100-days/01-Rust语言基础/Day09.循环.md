# Day09 循环

> **前置知识**：Day03 变量与可变性、Day08 控制流 if
> **难度**：入门
> **预计阅读时间**：20 分钟
> **目标**：掌握 `loop`、`while`、`for` 三种循环，知道什么时候用哪个，以及 `break` 怎么从循环里带回一个值。
> **代码说明**：本章每个例子都是自带 `main` 的完整程序，可以直接复制去跑，也可以覆盖同一份 `main.rs`。
> 每块前面标了 `📦`（能单独跑）。

---

## 三种循环怎么选

Rust 提供三种循环，各有各的场合：

| 循环 | 特点 | 什么时候用 |
|---|---|---|
| `loop` | 一直转，直到你 `break` | 不知道要转多少次，靠内部条件退出 |
| `while` | 条件成立就继续转 | 知道“满足某条件才接着转” |
| `for` | 把一组东西挨个过一遍 | 最常用，遍历数组、数字区间 |

下面一个一个看。

## loop：一直转，靠 break 停

`loop { ... }` 会不停重复执行花括号里的代码，除非你在里面写 `break`：

📦 本块能单独跑：

```rust
fn main() {
    let mut count = 0;

    loop {
        count += 1;
        if count == 3 {
            println!("到 3 了，退出");
            break;            // 跳出循环
        }
        println!("第 {} 次", count);
    }
}
```

`break` 是个关键字，意思是“立刻跳出当前这个循环”。`count` 前面写了 `mut`，因为在循环里要改它（Day03 讲过，Rust 变量默认不能改）。

> 没有 `break` 的 `loop` 是死循环，程序会一直卡在那儿。真要写“永远运行”的东西（比如服务器的主循环），才会故意不写 `break`。

### loop 能带回一个值

`loop` 也是表达式。在 `break` 后面跟一个值，这个值就作为整个 `loop` 的结果：

📦 本块能单独跑：

```rust
fn main() {
    let mut counter = 0;

    let result = loop {
        counter += 1;
        if counter == 10 {
            break counter * 2;    // break 带个值，作为 loop 的结果
        }
    };                          // 注意这里的分号

    println!("结果是 {}", result);   // 20
}
```

“反复尝试直到成功”这类场景很吃这个特性。比如一直读用户输入，读到合格的内容才 `break` 带出来。

## while：条件循环

`while` 是“每一轮开始前先看一眼条件，条件为真才继续”：

📦 本块能单独跑：

```rust
fn main() {
    let mut number = 3;

    while number != 0 {
        println!("{}！", number);
        number -= 1;        // 别忘了让条件最终变假，否则是死循环
    }

    println!("发射！");
}
```

输出：

```
3！
2！
1！
发射！
```

`while` 适合“不知道要转几圈，但有一个明确条件决定停不停”的场合。

## for：遍历循环（最常用）

要做“把一组东西挨个过一遍”的时候（数组的每个元素、1 到 10 每个数字），用 `for`。它不用你管下标，也就不会出现越界。

### 遍历数组

📦 本块能单独跑：

```rust
fn main() {
    let fruits = ["苹果", "香蕉", "橙子"];

    for fruit in fruits {
        println!("我爱吃 {}", fruit);
    }
}
```

`for 变量 in 一堆东西 { ... }` 是固定写法。`fruit` 是这一轮拿到的元素，第一轮是“苹果”，第二轮“香蕉”，第三轮“橙子”，一轮一个，取完了循环自己结束。

### 遍历数字区间

用 `a..b` 写一个数字范围，它的意思是**左闭右开**：算上 `a`，不算 `b`。

📦 本块能单独跑：

```rust
fn main() {
    for i in 0..5 {        // 0,1,2,3,4，没有 5
        println!("i = {}", i);
    }
}
```

想让右边那个数也算上，就用 `a..=b`，多了一个等号：

📦 本块能单独跑：

```rust
fn main() {
    for i in 1..=3 {       // 1,2,3
        println!("倒数 {}", 4 - i);
    }
}
```

> 对比：`0..5` 转 5 次（0 到 4），`0..=5` 转 6 次（0 到 5）。写循环次数时最容易在这里差一个。

### 倒着数

想从大到小，用 `.rev()`：

📦 本块能单独跑：

```rust
fn main() {
    for i in (1..=5).rev() {   // 5,4,3,2,1
        println!("{}", i);
    }
}
```

`.rev()` 是 reverse（倒转）的缩写，加在范围后面就把它反过来。外面那对括号不能省，省掉就变成对数字 `5` 调用 `.rev()` 了，编译器不认识这种写法，它会告诉你：

```
error[E0689]: can't call method `rev` on type `{integer}`
 --> src\main.rs:2:20
  |
2 |     for i in 1..=5.rev() {
  |                    ^^^ can't call method `rev` on type `{integer}`
  |
help: you must surround the range in parentheses to call its `rev` function
  |
2 |     for i in (1..=5).rev() {
  |              +     +
```

## break 和 continue

- `break`：立刻**跳出整个循环**。
- `continue`：**跳过本轮剩下的代码**，直接开始下一轮。

📦 本块能单独跑：

```rust
fn main() {
    for i in 1..=5 {
        if i == 3 {
            continue;        // 3 不打印，直接开始下一轮
        }
        if i == 5 {
            break;           // 到 5 直接结束循环
        }
        println!("{}", i);   // 只打印 1、2、4
    }
}
```

运行看看输出：

```powershell
cargo run
```

## 今日小结

- `loop { ... }` 无限循环，靠 `break` 退出；`break 值` 还能把这个值作为 `loop` 的结果。
- `while 条件 { ... }` 条件成立才接着转；记得让条件最终变假。
- `for 变量 in 区间/数组 { ... }` 最常用，不用管下标，不会越界。
- 区间 `a..b` 左闭右开（不含 `b`），`a..=b` 含两端；倒序用 `(a..=b).rev()`。
- `continue` 跳过本轮，`break` 跳出整个循环。

## 动手任务

1. 用 `for` 遍历 `1..=100`，打印所有偶数（提示：`if i % 2 == 0`）。
2. 用 `loop` 改写：从 1 累加到 10，累加到 10 时 `break`，并把累加和作为 `loop` 的结果打印出来。
3. 用 `while` 实现：从 10 倒数到 1，然后打印“发射！”。

**配合阅读**：[官方书 · 第3章 控制流（循环）](https://rustwiki.org/zh-CN/book/ch03-05-control-flow.html)
