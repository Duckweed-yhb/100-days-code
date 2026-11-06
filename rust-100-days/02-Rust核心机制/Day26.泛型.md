# Day26 泛型

> **前置知识**：Day12 切片、Day16 结构体、Day18 Option 与空值、Day22 向量 Vec
> **难度**：入门
> **预计阅读时间**：20 分钟
> **代码说明**：本章代码都放在**同一个** `main.rs` 里，按顺序往下加。标了 🧩 的块是在上一块基础上改的，同名类型别重复定义。
> 每块前面标了 `📦`（能单独跑）或 `🧩`（依赖上面的块，别单拎出来）。

目标：学会用泛型写「一份代码适用于多种类型」的函数、结构体和枚举，不用为每种类型重写一遍逻辑。

---

## 为什么需要泛型

写个函数，找出一个列表里的最大值。先只支持 `i32`：

📦 本块能单独跑：

```rust
fn largest_i32(list: &[i32]) -> i32 {
    let mut largest = list[0]; // 先拿第一个当擂主，这里假设列表非空
    for &item in list {
        if item > largest {
            largest = item;
        }
    }
    largest
}

fn main() {
    let numbers = vec![34, 50, 25, 100, 65];
    println!("最大数字：{}", largest_i32(&numbers));
}
```

`&[i32]` 是切片（Day12 讲过），`&numbers` 把 `Vec<i32>` 借成切片传进去，函数就不必关心你手里是 `Vec` 还是数组。

现在想找 `char` 列表的最大值，只能再抄一份，把 `i32` 全换成 `char`：

🧩 本块接上面第 1 块（同一个函数又抄了一遍，只换了类型）：

```rust
fn largest_char(list: &[char]) -> char {
    let mut largest = list[0];
    for &item in list {
        if item > largest {
            largest = item;
        }
    }
    largest
}
```

两份代码逻辑一字不差，只有类型不同。换成 `f64` 还得再抄一遍。

**泛型（generic）** 就是来消掉这种复制的：把类型本身变成参数，用一个占位符 `T` 代表任意类型，等你真正调用时再由编译器替换成具体类型。

`T` 取自 type 的首字母，叫**类型参数**，声明在尖括号 `<T>` 里。尖括号就是声明类型参数的位置，看到它就知道里面的名字是先占个位、后面才定的。

---

## 泛型函数

在函数名后面写 `<T>`：

📦 本块能单独跑：

```rust
// 对 i32 和 char 都能用的「找最大值」
fn largest<T: PartialOrd + Copy>(list: &[T]) -> T {
    let mut largest = list[0];
    for &item in list {
        if item > largest { // 要求 T 能比较
            largest = item;
        }
    }
    largest
}

fn main() {
    let numbers = vec![34, 50, 25, 100, 65];
    println!("最大数字：{}", largest(&numbers)); // 100

    let chars = vec!['y', 'm', 'a', 'q'];
    println!("最大字符：{}", largest(&chars)); // y
}
```

`<T: PartialOrd + Copy>` 叫 **trait bound（trait 约束）**。它规定 T 必须实现两个 trait：`PartialOrd` 负责比较，`Copy` 负责按值复制出元素。函数体里既用了 `>`，又从列表里按值取了 `item`，编译器必须确认 T 支持这两件事，这份泛型代码才能编过。trait 是 Day27 的主题，今天先照这个写法用。

`&[T]` 和返回值 `T` 里的 `T` 是同一个类型：传进去 `&[i32]`，拿回来就是 `i32`。

---

## 泛型结构体

结构体名后面加 `<T>`，字段里就能用它：

📦 本块能单独跑：

```rust
// x 和 y 必须是同一种类型 T
struct Point<T> {
    x: T,
    y: T,
}

// 两个类型参数：x 和 y 可以是不同类型
struct Point2<T, U> {
    x: T,
    y: U,
}

fn main() {
    let p = Point { x: 5, y: 10 };      // T = i32
    let q = Point { x: 1.0, y: 4.0 };   // T = f64
    let r = Point2 { x: 5, y: 4.0 };    // T = i32, U = f64

    println!("{} {} {}", p.x, q.y, r.x);
}
```

`Point` 的 x 和 y 共用一个 `T`，所以 `Point { x: 5, y: 4.0 }` 编不过：一边是 `i32`，一边是 `f64`。要混着放就用 `Point2`，两个类型参数各管各的。

---

## 泛型枚举

标准库里最常用的两个枚举都是泛型的。下面是它们的定义：

⚠️ 本块是 `std` 的源码，给你看定义用的，不要抄进自己的文件。抄了会盖住标准库里的同名 `Option`，报的错很难看懂（Day17 踩过这个坑）：

```rust
pub enum Option<T> {
    Some(T), // 有值，里面包着一个 T
    None,    // 没有值
}

pub enum Result<T, E> {
    Ok(T),
    Err(E),
}
```

用法你早就见过了，`Some(5)` 里那个 5 决定了 T 具体是什么：

📦 本块能单独跑：

```rust
fn main() {
    let some_num: Option<i32> = Some(5);
    let absent: Option<i32> = None; // 只写 None 推不出类型，得标注
    let ok: Result<i32, String> = Ok(7);

    println!("{:?} {:?} {:?}", some_num, absent, ok);
}
```

`{:?}` 是调试格式，需要类型实现 `Debug`，Day17 打印 `Color` 时用过它。`Option<i32>`、`Option<String>`、`Result<File, io::Error>` 都是同一个泛型枚举套上不同的具体类型。

---

## 泛型方法

给泛型结构体写方法，`impl` 后面也要跟一个 `<T>`：

🧩 本块接上面第 4 块（`Point` 已经定义过了，这里只加方法；块里的 `main` 替换掉上一块的 `main`）：

```rust
impl<T> Point<T> {
    fn x(&self) -> &T { // 返回字段的引用，所以是 &T
        &self.x
    }
}

// 只为 Point<f32> 实现，Point<i32> 没有这个方法
impl Point<f32> {
    fn distance_from_origin(&self) -> f32 {
        (self.x.powi(2) + self.y.powi(2)).sqrt()
    }
}

fn main() {
    let p = Point { x: 1.0, y: 4.0 };  // Point<f32>
    println!("x = {}", p.x());
    println!("到原点距离 = {}", p.distance_from_origin());

    let q = Point { x: 1, y: 4 };      // Point<i32>，只有 x() 能用
    println!("x = {}", q.x());
}
```

`impl<T> Point<T>` 里的 `T` 是新声明的，跟结构体上那个 `<T>` 没关系，只是碰巧同名。`impl Point<f32>` 没写 `<T>`，因为它只服务 `f32` 这一种具体类型，这样 `Point<i32>` 就分不到 `distance_from_origin`。

---

## 泛型不影响性能

新手常担心泛型会像 Java 那样带来运行时开销。Rust 这边不会。编译器在编译期把泛型代码针对每个具体类型各生成一份，这个动作叫**单态化（monomorphization）**：你写了 `largest<T>`，编译器替你生成 `largest_i32`、`largest_char` 这样的具体版本。运行时跑的机器码和手写两份函数一样，没有额外开销。

---

## 今日小结

- 泛型把类型参数化，一份逻辑适配多种类型，省掉复制粘贴
- 泛型函数：`fn largest<T: 约束>(list: &[T]) -> T`
- 泛型结构体/枚举：`struct Point<T> { x: T }`；多参数写 `Point2<T, U>`
- `impl<T> Point<T>` 才能在方法里用 T；也能只给具体类型（如 `Point<f32>`）实现方法
- 标准库的 `Option<T>`、`Result<T, E>` 都是泛型；编译期单态化，运行时零开销

---

## 动手任务

1. 写一个泛型函数 `fn print_slice<T: std::fmt::Debug>(s: &[T])`，用 `{:?}` 打印整个 slice。
2. 定义泛型结构体 `Pair<T> { first: T, second: T }`，为它写一个关联函数 `fn new(first: T, second: T) -> Self`。
3. 分别用 `Pair { first: 1, second: 2 }` 和 `Pair { first: "a", second: "b" }` 造实例，确认同一份结构体代码能套不同类型。

> 提示：想用 `{:?}` 打印 T，bound 里必须写 `T: std::fmt::Debug`，不然编译器不知道怎么格式化。`Self` 是「当前这个类型」的简写，写在 `impl Pair<T>` 里就代表 `Pair<T>`。

---

**配合阅读**：[官方书·第10章 泛型数据类型](https://rustwiki.org/zh-CN/book/ch10-01-syntax.html)
