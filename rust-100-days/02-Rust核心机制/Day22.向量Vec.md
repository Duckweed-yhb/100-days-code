# Day22 向量 Vec

> **前置知识**：Day11 引用与借用（`&v[i]`、`&mut v`）、Day17 枚举（Vec 装混合类型要靠它）、Day18 Option（`get()` 和 `pop()` 的返回值）
> **难度**：入门
> **预计阅读时间**：20 分钟
> **代码说明**：本章代码都放在**同一个** `main.rs` 里。每块都带完整的 `fn main()`，单独复制到空文件里就能 `cargo run`。
> 每块前面都标了 `📦`（能单独跑）。两块的 `main` 不能同时塞进一个文件，看哪块就换哪块。

---

## Vec 是什么

数组的长度在创建时就定死了，不能加长。可很多时候你不知道要存多少个：读一遍文件里所有的单词、收集用户每次输入的数字，都得先攒着再说。Rust 给这种场景准备了**向量 `Vec<T>`**，一个能动态增删元素的可变长数组，数据放在堆上。

`<T>` 里的 `T` 是类型占位符，术语叫**泛型**。符号上可以理解成「装什么」的参数：写 `Vec<i32>` 就是「装 i32 的 Vec」。Day26 会专门讲泛型，今天照着写就行。一个 Vec 里所有元素必须是同一种类型。

## 创建 Vec

📦 本块能单独跑：

```rust
fn main() {
    // 方式一：先建空向量，再用 push 往里加
    let mut v: Vec<i32> = Vec::new();
    v.push(1);
    v.push(2);
    println!("{:?}", v);  // [1, 2]

    // 方式二：vec! 宏，直接把初始元素列出来
    let v2 = vec![10, 20, 30];
    println!("{:?}", v2); // [10, 20, 30]
}
```

`{:?}` 是调试格式的占位符（Day17 用过）。`{}` 只能打印单个值，向量、数组这种一整块的数据得用 `{:?}`。

方式一里 `Vec<i32>` 的类型不能省。`Vec::new()` 刚造出来时里面一个元素都没有，编译器没线索，推断不出 `T` 是什么。写完 `push(1)` 就够它推断了，但标出来更清楚。

变量前面必须加 `mut`，因为 `push` 要改内容。Day03 讲过，Rust 的变量默认不可变。

## 读取元素的两种方式

### 用 `[]` 下标，越界直接 panic

📦 本块能单独跑：

```rust
fn main() {
    let v = vec![10, 20, 30];
    let third = &v[2]; // 下标从 0 开始，2 是第三个元素
    println!("第三个元素是 {}", third);

    // let out = &v[99]; // 把这行放出来：运行时 panic，index out of bounds
}
```

`&v[2]` 前面的 `&` 是借用，拿到的是元素的引用而不是元素本身（Day11 的规则）。

### 用 `get()`，越界返回 None

`get()` 收一个下标，返回 `Option<&T>`，也就是 Day18 学的那个枚举：有就给 `Some`，越界就给 `None`，程序不会崩。

📦 本块能单独跑：

```rust
fn main() {
    let v = vec![10, 20, 30];

    match v.get(2) {
        Some(val) => println!("第三个元素是 {}", val), // 走这里
        None => println!("下标越界了"),
    }

    match v.get(99) {
        Some(val) => println!("{}", val),
        None => println!("下标越界了"), // 走这里，不崩
    }
}
```

`Option<&T>` 解出来的 `val` 是 `&i32`，打印时直接写 `{}` 就行。

> 借用的限制：`let third = &v[2];` 是不可变借用。只要 `third` 还活着，就不能再对 `v` 做 `push`。push 一旦触发扩容，Vec 的数据会搬到堆上另一块内存，`third` 指的还是旧地址。

## 修改用 push 和 pop

📦 本块能单独跑：

```rust
fn main() {
    let mut v = Vec::new();
    v.push(1);
    v.push(2);
    v.push(3);

    // pop 从末尾弹出一个，返回 Option<T>：还有元素给 Some，空了给 None
    let last = v.pop();
    println!("弹出 {:?}, 剩下 {:?}", last, v); // Some(3), [1, 2]
}
```

这里的 `v` 没有标类型，因为连着 `push(1)`、`push(2)` 之后编译器已经知道它是 `Vec<i32>`。

## 遍历

📦 本块能单独跑：

```rust
fn main() {
    let v = vec![100, 200, 300];

    // &v：不可变借用，只读
    for num in &v {
        println!("{}", num); // num 是 &i32
    }

    // &mut v：可变借用，能改元素
    let mut nums = vec![1, 2, 3];
    for num in &mut nums {
        *num *= 10; // * 是解引用，取出 num 指向的那个值，乘 10 再存回去
    }
    println!("{:?}", nums); // [10, 20, 30]
}
```

`&mut nums` 遍历出来的 `num` 类型是 `&mut i32`，一个指向元素的可变引用。要改的是它指向的那个 `i32`，所以得写 `*num`，前面那个 `*` 就是解引用。

## 常用方法速查

| 操作 | 写法 | 说明 |
|---|---|---|
| 新建空 | `Vec::new()` | 常配 `push`，要标类型 |
| 宏初始化 | `vec![1, 2, 3]` | 推荐 |
| 末尾加 | `v.push(x)` | 变量得是 `mut` |
| 末尾弹 | `v.pop()` | 返回 `Option<T>` |
| 长度 | `v.len()` | 元素个数 |
| 是否为空 | `v.is_empty()` | 返回 bool |
| 清空 | `v.clear()` | 删掉所有元素 |
| 按下标取 | `&v[i]` | 越界 panic |
| 安全取 | `v.get(i)` | 返回 `Option<&T>` |
| 包含？ | `v.contains(&x)` | 返回 bool |

## Vec 放不同类型？套一层枚举

一个 Vec 只能放同一种类型，这条规则没变。想放「可能是数字、可能是文字」的混合数据，就先用枚举把它们变成同一个类型（Day17 的套路）：

📦 本块能单独跑：

```rust
enum Cell {
    Int(i32),
    Text(String),
}

fn main() {
    let row = vec![
        Cell::Int(3),
        Cell::Text(String::from("你好")),
        Cell::Int(42),
    ];

    for c in &row {
        match c {
            Cell::Int(n) => println!("数字 {}", n),
            Cell::Text(s) => println!("文字 {}", s),
        }
    }
}
```

`&row` 遍历出来的 `c` 是 `&Cell`，可 match 的分支里直接写 `Cell::Int(n)` 就能把值取出来，不用手写 `&`。这是 Day19 讲的匹配规则，编译器会自动帮你补上那一层。

## 内存小知识

Vec 的数据在堆上，它自己在栈上占三个机器字：**指向堆的指针、长度 `len`、容量 `capacity`**。`len` 是当前有几个元素，`capacity` 是这块内存最多装几个。`push` 超过容量时，Vec 会申请一块更大的内存，把旧数据搬过去。

频繁 push 不用你操心，它自己会扩容。如果你提前知道大概要存多少，用 `Vec::with_capacity(1000)` 一次留够，能少搬几趟。

## 今日小结

- `Vec<T>` 是堆上的可变长数组，元素类型必须相同，下标从 0 开始
- 创建：`Vec::new()` 配 `push`（要标类型），或者 `vec![1, 2, 3]`
- 读取：`&v[i]` 越界 panic；`v.get(i)` 返回 `Option<&T>`，安全
- 修改：`push` 末尾加、`pop` 末尾弹；`&mut v` 遍历要写 `*num` 才改得动元素
- 混合类型用枚举包一层，装进同一个 Vec

## 动手任务

1. 用 `vec!` 造一个 `vec![2, 4, 6, 8]`，`for` 遍历求和并打印。
2. 造一个空 `Vec<i32>`，循环 `push` 1 到 5，再用 `pop()` 依次弹出并打印，看看顺序是不是倒的。
3. 用 `v.get(100)` 越界访问，match 出 `Some` / `None` 两个分支，确认程序不崩。

> 第 1 题提示：`for num in &v` 里 `num` 是 `&i32`，`{}` 直接打得出来。
> 第 3 题提示：越界时 `get` 给的是 `None`，所以 `Some` 那个分支不会走到，这是正常的。

**配合阅读**：[官方书·第8章 使用向量](https://rustwiki.org/zh-CN/book/ch08-01-vectors.html)
