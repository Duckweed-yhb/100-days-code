# Day08 控制流 if

> **前置知识**：Day03 变量与可变性、Day05 运算符、Day06 函数
> **难度**：入门
> **预计阅读时间**：18 分钟
> **目标**：用 `if / else if / else` 做分支判断，并理解 Rust 的 `if` 是一个能返回值的表达式。
> **代码说明**：本章代码都放在**同一个** `main.rs` 里，按顺序往下加就能跑。
> 每块前面标了 `📦`（能单独跑）、`🧩`（依赖上面的块）或 `⚠️`（只给你看，故意编不过，别照抄）。

---

## if 是做什么的

程序不一定从上到下一路走到底。很多时候得看情况办事：成绩及格就打印“通过”，否则打印“补考”。这种“条件成立才走某条路”的逻辑，靠 `if`。

## 最基本的 if / else

📦 本块能单独跑：

```rust
fn main() {
    let score = 76;

    if score >= 60 {
        println!("恭喜，通过了！");
    } else {
        println!("很遗憾，需要补考");
    }
}
```

`if` 后面跟的是**条件**，条件后面直接跟花括号包起来的分支；条件不成立时走 `else` 那个分支。

Rust 的 `if` 有两点和 C、Java 不一样：

1. **条件外面不用写括号**。写成 `if (score >= 60)` 也能编过，只是多余。
2. **条件必须是 `bool`**。Rust 不会把数字 `0`、空字符串自动当成“假”。

第 2 条的错误长这样：

⚠️ 本块故意编不过（`1` 不是 `bool`），别照抄：

```rust
fn main() {
    if 1 {
        println!("不会执行");
    }
}
```

编译器的原话：

```
error[E0308]: mismatched types
 --> src\main.rs:2:8
  |
2 |     if 1 {
  |        ^ expected `bool`, found integer
```

条件得老老实实写成比较，比如 `if score >= 60`、`if 1 == 1`。比较运算出来的东西就是 `bool`（Day05 讲过）。

## 多分支 else if

超过两种情况时，用 `else if` 依次判断：

📦 本块能单独跑：

```rust
fn main() {
    let score = 85;

    if score >= 90 {
        println!("优秀");
    } else if score >= 80 {
        println!("良好");
    } else if score >= 60 {
        println!("及格");
    } else {
        println!("不及格");
    }
}
```

执行顺序是从上往下判断，**命中第一个成立的分支就不再往下走**；全都不成立才走最后的 `else`。

> `else if` 堆多了会显得啰嗦。Day19 会学 `match` 表达式，那种多分支匹配的场景用 `match` 更清爽，现在先用 `if`。

## if 是一个表达式

这是 Rust 的一个特点：**`if` 会产生一个值**。整个 `if ... else ...` 可以当成一个值赋给变量。

📦 本块能单独跑：

```rust
fn main() {
    let age = 20;

    let category = if age >= 18 {
        "成年人"      // 注意：没有分号
    } else {
        "未成年人"
    };               // 整个 if 到这里结束，这里有分号

    println!("{}", category);
}
```

规矩和 Day06 的函数返回值是同一套：分支块里最后一个**不带分号**的表达式，就是这个分支的值。

### 两个分支的类型必须一致

既然 `if` 要产出一个值，各分支产出的类型就得一样，不然编译器直接拦下来：

⚠️ 本块故意编不过（一个分支是 `i32`，另一个是 `&str`），别照抄：

```rust
fn main() {
    let x = if true {
        5
    } else {
        "six"
    };
    println!("{}", x);
}
```

编译器的原话：

```
error[E0308]: `if` and `else` have incompatible types
 --> src\main.rs:5:9
  |
2 |       let x = if true {
  |  _____________-
3 | |         5
  | |         - expected because of this
4 | |     } else {
5 | |         "six"
  | |         ^^^^^ expected integer, found `&str`
6 | |     };
  | |_____- `if` and `else` have incompatible types
```

两边都写成整数就没事：

📦 本块能单独跑：

```rust
fn main() {
    let x = if true { 5 } else { 6 };   // ✅ 两边都是 i32
    println!("{}", x);
}
```

## 用 let + if 写二选一

根据条件在两个值里挑一个，用 `let` 接住 `if` 表达式就很短。比如求两个数的较大值：

📦 本块能单独跑：

```rust
fn main() {
    let a = 30;
    let b = 50;
    let bigger = if a > b { a } else { b };
    println!("较大的数是 {}", bigger);
}
```

运行看看输出：

```powershell
cargo run
```

## 今日小结

- `if 条件 { ... } else if 条件 { ... } else { ... }`，条件外面不用括号，但**必须是 `bool`**。
- 判断从上往下走，命中第一个成立的分支就停；都不命中走 `else`。
- `if` 是表达式，能产出值；把它赋给变量时，**各分支类型必须一致**。
- 分支块里靠“不带分号的最后一个表达式”产出值。

## 动手任务

1. 写程序：给定一个成绩 `score`（自己设值），用 `if / else if / else` 打印“优秀 / 良好 / 及格 / 不及格”。
2. 用 `let 变量 = if ... else ...` 求三个数里的最大值（提示：嵌套两个 `if`）。
3. 故意写一段 `if 0 { ... }`，看看编译器怎么报错，体会“条件必须是 `bool`”。

**配合阅读**：[官方书 · 第3章 控制流（if 表达式）](https://rustwiki.org/zh-CN/book/ch03-05-control-flow.html)
