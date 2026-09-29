# Day09 循环

> 目标：掌握 Rust 的三种循环——`loop`、`while`、`for`，知道什么时候用哪个，以及 `break` 怎么从循环里返回值。

## 三种循环怎么选

Rust 提供三种循环，它们各有适用场景：

| 循环 | 特点 | 什么时候用 |
|---|---|---|
| `loop` | 一直转，直到你 `break` | 不知道要转多少次，靠内部条件退出 |
| `while` | 条件成立就继续 | 知道"满足某条件才接着转" |
| `for` | 遍历一个集合或区间 | 最常用，遍历数组/数字范围 |

下面一个一个看。

## loop：无限循环 + break

`loop { ... }` 会**不停地重复执行花括号里的代码**，除非你在里面写 `break` 让它停下来：

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

> 没有 `break` 的 `loop` 是死循环，程序会一直卡着。如果你真的想要"永远运行"（比如服务器主循环），那另说。

### loop 还能返回值（特色）

`loop` 也是表达式！你可以在 `break` 后面带一个值，这个值就作为整个 `loop` 的结果返回出来：

```rust
fn main() {
    let mut counter = 0;

    let result = loop {
        counter += 1;
        if counter == 10 {
            break counter * 2;    // break 带个值，作为 loop 的返回值
        }
    };                          // 注意这里分号

    println!("结果是 {}", result);   // 20
}
```

这个特性在"反复尝试直到成功"的场景特别好用，比如不停地读用户输入直到合法。

## while：条件循环

`while` 是"每次转之前先看一眼条件，条件为真才继续"：

```rust
fn main() {
    let mut number = 3;

    while number != 0 {
        println!("{}！", number);
        number -= 1;        // 别忘了让条件 eventually 变假，否则死循环
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

适合"不知道具体次数，但靠一个条件控制"的场景。

## for：遍历循环（最常用）

绝大多数时候，你要做的是"把一组东西挨个过一遍"，比如数组的每个元素、或从 1 数到 10。这时候用 `for`，它最安全也最常用。

### 遍历数组

```rust
fn main() {
    let fruits = ["苹果", "香蕉", "橙子"];

    for fruit in fruits {
        println!("我爱吃 {}", fruit);
    }
}
```

`fruit` 是每次循环拿到的元素，自动按顺序取，不用你管索引，也就不会越界。

### 遍历数字区间

用 `a..b` 表示一个**左闭右开**的区间（包含 a，不包含 b）：

```rust
fn main() {
    for i in 0..5 {        // 0,1,2,3,4 —— 注意没有 5
        println!("i = {}", i);
    }
}
```

想**包含**右端点，用 `a..=b`：

```rust
fn main() {
    for i in 1..=3 {       // 1,2,3
        println!("倒数 {}", 4 - i);
    }
}
```

> 对比：`0..5` 转 5 次（0 到 4）；`0..=5` 转 6 次（0 到 5）。写循环次数时特别容易搞错边界，多留心。

### 倒着数

想从大到小，用 `.rev()`（reverse 倒转）：

```rust
fn main() {
    for i in (1..=5).rev() {   // 5,4,3,2,1
        println!("{}", i);
    }
}
```

## break 和 continue

- `break`：立刻**跳出整个循环**。
- `continue`：**跳过本次**剩下的代码，直接进入下一次循环。

```rust
fn main() {
    for i in 1..=5 {
        if i == 3 {
            continue;        // 3 不打印，直接下一轮
        }
        if i == 5 {
            break;           // 到 5 直接结束循环
        }
        println!("{}", i);   // 只打印 1,2,4
    }
}
```

运行下面命令看看输出：

```powershell
cargo run
```

## 今日小结

- `loop { ... }` 无限循环，靠 `break` 退出；`break 值` 还能把值作为 loop 的返回结果。
- `while 条件 { ... }` 条件成立才循环；记得让条件最终变假。
- `for x in 区间/数组 { ... }` 最常用，安全不越界。
- 区间 `a..b` 左闭右开（不含 b），`a..=b` 含两端；倒序用 `.rev()`。
- `continue` 跳过本次，`break` 跳出整个循环。

## 动手任务

1. 用 `for` 遍历 `1..=100`，打印所有偶数（提示：`if i % 2 == 0`）。
2. 用 `loop` 改写：从 1 累加到 10，遇到 10 时 `break` 并把累加和作为返回值打印出来。
3. 用 `while` 实现：从 10 倒数到 1，再打印"发射！"。

**配合阅读**：[官方书·第3章 控制流（循环）](https://rustwiki.org/zh-CN/book/ch03-05-control-flow.html)
