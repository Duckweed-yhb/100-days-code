# Day26 泛型

> 目标：学会用泛型写「一份代码适用于多种类型」的函数、结构体和枚举，避免为每种类型重复写一遍逻辑。

## 为什么需要泛型

假设你要写个函数，找一个列表里的最大值。先为 `i32` 写一个：

```rust
fn largest_i32(list: &[i32]) -> i32 {
    let mut largest = list[0];
    for &item in list {
        if item > largest {
            largest = item;
        }
    }
    largest
}
```

现在还想找 `char` 列表的最大值？你只能照抄一份，把 `i32` 全换成 `char`——逻辑一模一样，只是类型不同。这种复制粘贴就是泛型要消灭的东西。

**泛型（generic）**就是把「类型」也变成一个参数。你写一份逻辑，用一个占位符 `T` 代表「任意类型」，编译器在你实际使用时再把它具体化。

## 泛型函数

在函数名后面用 `<T>` 声明类型参数：

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
    println!("最大数字: {}", largest(&numbers)); // 100

    let chars = vec!['y', 'm', 'a', 'q'];
    println!("最大字符: {}", largest(&chars)); // y
}
```

那个 `<T: PartialOrd + Copy>` 叫 **trait bound（ trait 约束）**，意思是：T 必须实现「可比较（PartialOrd）」和「可复制（Copy）」两个 trait。为什么要写？因为函数体里用了 `>` 比较、用了从列表里复制出值——编译器得保证 T 真的支持这些操作。trait 明天 Day27 细讲，今天先照这个写法用。

## 泛型结构体

结构体名后加 `<T>`，字段里就能用它：

```rust
// x 和 y 必须是同一种类型 T
struct Point<T> {
    x: T,
    y: T,
}

// 多个泛型参数：x 和 y 可以是不同类型
struct Point2<T, U> {
    x: T,
    y: U,
}

fn main() {
    let p = Point { x: 5, y: 10 };       // T = i32
    let q = Point { x: 1.0, y: 4.0 };    // T = f64
    let r = Point2 { x: 5, y: 4.0 };     // T=i32, U=f64
}
```

## 泛型枚举

你其实早就用过标准库的泛型枚举了：

```rust
enum Option<T> {
    Some(T),
    None,
}

enum Result<T, E> {
    Ok(T),
    Err(E),
}
```

`Option<i32>`、`Option<String>`、`Result<File, io::Error>`——都是同一个泛型枚举套上不同具体类型。

## 泛型方法

给泛型结构体写方法时，要在 `impl` 后面也声明 `<T>`：

```rust
struct Point<T> {
    x: T,
    y: T,
}

impl<T> Point<T> {
    // 方法里能使用 T
    fn x(&self) -> &T {
        &self.x
    }
}

// 也可以只为具体类型 f32 实现方法——Point<i32> 就没有这个方法
impl Point<f32> {
    fn distance_from_origin(&self) -> f32 {
        (self.x.powi(2) + self.y.powi(2)).sqrt()
    }
}
```

## 泛型不影响性能

新手常担心：泛型是不是像 Java 那样有运行时开销？**不是。** Rust 在编译期会把泛型代码针对每个具体类型**单态化（monomorphization）**——也就是编译器帮你生成 `largest_i32`、`largest_char` 这种具体版本。运行时跑的就是和手写一遍一模一样的机器码，**零成本**。

## 今日小结

- 泛型把「类型」参数化，一份逻辑适配多种类型，避免复制粘贴
- 泛型函数：`fn largest<T: 约束>(list: &[T]) -> T`
- 泛型结构体/枚举：`struct Point<T> { x: T }`；多参数写 `Point2<T, U>`
- `impl<T> Point<T>` 才能在方法里用 T；也能只给具体类型（如 `Point<f32>`）实现方法
- 标准库的 `Option<T>`、`Result<T,E>` 都是泛型；编译期单态化，运行时零开销

## 动手任务

1. 写一个泛型函数 `fn print_slice<T: std::fmt::Debug>(s: &[T])`，用 `{:?}` 打印整个 slice（提示：trait bound 加 `Debug`）。
2. 定义泛型结构体 `Pair<T> { first: T, second: T }`，为它写一个方法 `fn new(first: T, second: T) -> Self`。
3. 分别用 `Pair { first: 1, second: 2 }` 和 `Pair { first: "a", second: "b" }` 造实例，确认同一份结构体代码能套不同类型。

> 提示：想 `{:?}` 打印 T，必须在 bound 里写 `T: std::fmt::Debug`，否则编译器不知道怎么格式化。

**配合阅读**：[官方书·第10章 泛型数据类型](https://rustwiki.org/zh-CN/book/ch10-01-syntax.html)
