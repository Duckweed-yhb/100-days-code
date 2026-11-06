# Day20 if let 与 while let

> **前置知识**：Day18 Option 与空值处理（`Some` / `None` 必须拆开才能用）、Day19 match 模式匹配（分支、`_` 兜底、穷尽检查）
> **难度**：入门
> **预计阅读时间**：15 分钟
> **代码说明**：标 `📦` 的块是一个**完整程序**，整块贴进 `src/main.rs` 就能 `cargo run`。
> 每个块都自带 `main`，也别把两块摞进同一个文件，那样会报 `E0428`（名字重复）。换块的时候，把上一块整个替换掉。

---

## if let：只关心一个分支

Day19 的 `match` 很强大，但它有个麻烦：当你**只在意某一种情况**、其余情况都懒得处理时，还得配一个 `_ => ()` 兜底，写起来啰嗦。

比如只想看看值是不是 `Some(3)`：

📦 本块能单独跑：

```rust
fn main() {
    let some_value = Some(3);

    // 用 match：不关心的情况也得写出来
    match some_value {
        Some(3) => println!("是三！"),
        _ => (),   // 其余情况什么都不做，纯陪跑
    }

    // 用 if let：只写关心的那一支
    if let Some(3) = some_value {
        println!("这是 if let 说的：是三！");
    }
}
```

`_ => ()` 里的 `()` 是「单元类型」，值就是「什么都没有」，表示这里什么都不做。

`if let` 的读法是 `if let 模式 = 值 { ... }`：等号左边是**模式**（不是声明变量），右边是要检查的值，匹配上就执行大括号里的代码，匹配不上整块跳过。它和 `match` 一样能绑定值：

📦 本块能单独跑：

```rust
fn main() {
    let maybe_num: Option<i32> = Some(42);

    if let Some(n) = maybe_num {
        println!("拿到数字 {}", n);   // n 被绑定成 42
    }
}
```

## if let 配 else：处理另一种情况

`if let` 后面还能跟 `else`，相当于 `match` 里的 `_` 分支：

📦 本块能单独跑：

```rust
#[derive(Debug)]     // 下面要用 {:?} 打印州名
enum UsState { Alabama }

enum Coin { Penny, Quarter(UsState) }

fn main() {
    let mut count = 0;

    let coin = Coin::Penny;
    if let Coin::Quarter(state) = coin {
        println!("这枚夸脱来自 {:?} 州", state);
    } else {
        count += 1;              // 不是夸脱，计数器 +1
    }

    // 换一枚 25 美分的，这次会走 if 那一支
    let coin = Coin::Quarter(UsState::Alabama);
    if let Coin::Quarter(state) = coin {
        println!("这枚夸脱来自 {:?} 州", state);
    } else {
        count += 1;
    }

    println!("不是夸脱的枚数：{}", count);
}
```

## 一个权衡：简写 vs 穷尽检查

| | `match` | `if let` |
|---|---|---|
| 代码量 | 必须列全所有分支 | 只写关心的那一个，短 |
| 安全检查 | 编译器强制穷尽，漏了会报 `E0004` | **不检查穷尽**，漏了别的情况也没人拦 |
| 适合 | 要处理多种分支、需要编译器兜底 | 只在意一种情况，其余忽略 |

`if let` 用「放弃穷尽检查」换来了短一点的代码。要处理两三种以上情况时，还是老实写 `match`。

## while let：不断取值直到 None

还有一种常见循环：反复从一个集合里弹出一个值，只要有值就处理，直到变成 `None` 为止。这时候用 `while let`：

📦 本块能单独跑：

```rust
fn main() {
    // vec![1, 2, 3] 是标准库的宏，用来造一个 Vec（动态数组），Day22 会细讲
    let mut stack = vec![1, 2, 3];

    // 只要 pop() 返回 Some(x)，就把 x 处理掉；返回 None 时循环结束
    while let Some(top) = stack.pop() {
        println!("弹出了 {}", top);
    }

    println!("栈空了");
}
```

输出是 `3`、`2`、`1`，因为 `Vec::pop()` 从尾部弹出。它返回的就是 `Option<T>`：栈里有元素时返回 `Some(元素)`，空了返回 `None`。`while let` 的意思是「只要还能匹配上 `Some(top)`，就继续循环」。这里的 `stack` 必须声明成 `mut`，`pop` 会改动这个 `Vec`。

不用 `while let` 的话，就得这么写：

📦 本块能单独跑：

```rust
fn main() {
    let mut stack = vec![1, 2, 3];

    loop {
        match stack.pop() {
            Some(top) => println!("弹出了 {}", top),
            None => break,          // 空了就跳出循环
        }
    }

    println!("栈空了");
}
```

`loop` 是无限循环，`break` 跳出去（Day09 讲过）。两段代码效果一样，`while let` 只是把「弹出、判断、退出」压成了一行。

## 把几段拼起来跑一次

把下面这段整个贴进 `src/main.rs`，观察两部分的输出：

📦 本块能单独跑：

```rust
fn main() {
    // if let 这一半
    let score: Option<i32> = Some(100);
    if let Some(s) = score {
        println!("考试得了 {} 分", s);
    } else {
        println!("缺考");
    }

    // while let 这一半
    let mut nums = vec![10, 20, 30];
    while let Some(n) = nums.pop() {
        println!("剩余 {}", n);
    }
}
```

## 今日小结

- `if let 模式 = 值 { ... }` 是「只关心一个分支」时 `match` 的简写，同样能绑定值
- 后面可以接 `else`，作用相当于 `match` 的 `_` 分支
- 代价是失去 `match` 的穷尽性检查，多分支场景仍然用 `match`
- `while let 模式 = 表达式 { ... }` 用于「不断取值直到 `None`」的循环，典型写法 `while let Some(x) = v.pop()`

## 动手任务

**1.** 定义一个 `Option<String>`，用 `if let Some(s)` 判断它是不是等于 `"hello"`，是就打印「匹配成功」，不是就走 `else` 打印「不是 hello」。两个值都试一遍。

**2.** 造一个 `Vec`，放 `1`、`2`、`3`、`4`、`5`，用 `pop()` 配合 `while let` 把所有元素倒序打印出来。

**3.** 写 `let x: Option<i32> = None;`，再用 `if let Some(n) = x { ... } else { ... }`，看它走哪个分支（答案是 `else`，因为 `None` 匹配不上 `Some(n)`）。如果你偷懒写成裸的 `if let Some(n) = None`，编译器拦下来的会是 `E0282` 而不是进不进分支的问题，原因见 Day18 的踩坑框。

> 提示 1：`v.pop()` 里 `v` 必须是 `mut`，因为 `pop` 会修改 `Vec`。
> 提示 2：比较 `String` 和字符串字面量直接写 `s == "hello"` 就行，不用把两边转成同一种类型。

**配合阅读**：[官方书·第6章 if let 简单控制流](https://rustwiki.org/zh-CN/book/ch06-03-if-let.html)
