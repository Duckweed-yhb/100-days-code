# Day22 向量 Vec

> 目标：学会用 `Vec<T>` 这个可变长数组来存储一串同类型数据，掌握创建、增删、读取和遍历。

## Vec 是什么

数组的长度在创建时就固定了，不能加长。可很多时候你不知道要存多少个：比如读一遍文件里所有单词、收集用户每次输入的数字。这时就需要**向量 `Vec<T>`（vector）**——它是 Rust 的**可变长数组**，数据存在堆上，可以动态增删元素。

`<T>` 是泛型，表示 Vec 里装的是哪种类型（后面 Day26 会专门讲泛型，现在先照抄语法）。一个 Vec 里所有元素必须是同一种类型。

## 创建 Vec

两种常用方式：

```rust
// 方式一：Vec::new() 空向量，再用 push 往里加
let mut v: Vec<i32> = Vec::new();
v.push(1);
v.push(2);

// 方式二：vec! 宏（推荐，更简洁，直接给出初始元素）
let v2 = vec![10, 20, 30];
```

注意 `Vec::new()` 时如果还没往里面塞任何元素，编译器推断不出 `T` 是什么，所以要标注 `Vec<i32>`；一旦 `push` 了，类型就能推断出来。

## 读取元素：两种方式

### 用 `[]` 索引（越界会 panic）

```rust
let v = vec![10, 20, 30];
let third = &v[2]; // 取第三个元素的引用
println!("第三个元素是 {}", third);

// let out = &v[99]; // ❌ 运行时 panic！索引越界
```

### 用 `get()`（越界返回 Option，不 panic）

```rust
let v = vec![10, 20, 30];

match v.get(2) {
    Some(val) => println!("第三个元素是 {}", val), // 走这
    None => println!("索引越界了"),
}

match v.get(99) {
    Some(val) => println!("{}", val),
    None => println!("索引越界了"), // 走这，不崩溃
}
```

对比 Day18 学的：`[]` 越界直接 panic，`get()` 返回 `Option`，由你处理。索引可能越界时优先用 `get()`。

> 借用规则回顾：`let third = &v[2];` 是不可变借用后，你不能再 `v.push(4);`——因为 push 可能导致 Vec 重新分配内存，让那个引用悬垂。这正是所有权规则在帮你。

## 修改：push 与 pop

```rust
let mut v = Vec::new();
v.push(1);
v.push(2);
v.push(3);

// pop 从末尾弹出一个元素，返回 Option
let last = v.pop(); // Some(3)
println!("弹出 {:?}, 剩下 {:?}", last, v); // 剩下 [1, 2]
```

## 遍历

用 `for` 循环：

```rust
let v = vec![100, 200, 300];

// 不可变遍历：只读
for num in &v {
    println!("{}", num);
}

// 可变遍历：要修改每个元素
let mut v = vec![1, 2, 3];
for num in &mut v {
    *num *= 10; // *num 解引用，拿到元素本体再乘 10
}
println!("{:?}", v); // [10, 20, 30]
```

注意 `&mut v` 遍历时用 `*num *= 10`——`num` 是 `&mut i32`（对元素的可变引用），要先 `*` 解引用才能改到真实的值。

## Vec 也有方法清单

常用 API 速查：

| 操作 | 写法 | 说明 |
|---|---|---|
| 新建空 | `Vec::new()` | 常配 `push` |
| 宏初始化 | `vec![1, 2, 3]` | 推荐 |
| 末尾加 | `v.push(x)` | |
| 末尾弹 | `v.pop()` | 返回 `Option<T>` |
| 长度 | `v.len()` | |
| 是否为空 | `v.is_empty()` | |
| 清空 | `v.clear()` | 删掉所有元素 |
| 按索引取 | `&v[i]` | 越界 panic |
| 安全取 | `v.get(i)` | 返回 `Option<&T>` |
| 包含？ | `v.contains(&x)` | 返回 bool |

## Vec 放不同类型？用枚举

前面说一个 Vec 只能放同一种类型。但如果你真想放「可能是数字、可能是文字」的混合体怎么办？答案是用枚举统一成一个类型（回忆 Day17）：

```rust
enum Cell {
    Int(i32),
    Text(String),
}

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
```

这是「异构集合」在 Rust 里的标准做法，后面写解释器、状态机时会反复用到。

## 内存小知识

Vec 在堆上存数据，它自己在栈上其实只占三个词：**指向堆的指针、长度 len、容量 capacity**。当 `push` 超过容量时，它会在堆上申请一块更大的内存、把旧数据搬过去。所以频繁 push 没问题，但如果你知道大概要存多少，可以用 `Vec::with_capacity(1000)` 预留空间，减少重复搬移。

## 今日小结

- `Vec<T>` 是堆上的可变长数组，所有元素同类型
- 创建：`Vec::new()` + `push`，或更常用的 `vec![1,2,3]`
- 读取：`&v[i]`（越界 panic）或 `v.get(i)`（返回 `Option`，安全）
- 修改：`push` 末尾加、`pop` 末尾弹；`&mut v` 遍历配合 `*num` 改值
- 想存「混合类型」就用枚举把变体统一成一个类型

## 动手任务

1. 用 `vec!` 创建一个 `vec![2,4,6,8]`，用 `for` 遍历求和并打印。
2. 创建一个空 `Vec<i32>`，循环 `push` 1 到 5，然后用 `pop()` 把它们依次弹出并打印（观察是不是倒序）。
3. 用 `v.get(100)` 越界访问，匹配 `Some`/`None`，确认它不崩溃。

> 提示：`for num in &v` 里 `num` 是 `&i32`，打印时直接 `{}` 即可，Rust 会自动解引用。

**配合阅读**：[官方书·第8章 使用向量](https://rustwiki.org/zh-CN/book/ch08-01-vectors.html)
