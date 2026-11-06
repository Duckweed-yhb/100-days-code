# Day18 Option 与空值处理

> **前置知识**：Day16 结构体（`#[derive(Debug)]`、`impl` 块）、Day17 枚举（变体、`::`、`Option<T>` 长什么样）
> **难度**：入门
> **预计阅读时间**：15 分钟
> **代码说明**：标 `📦` 的块是一个**完整程序**，整块贴进 `src/main.rs` 就能 `cargo run`。
> 标 `⚠️` 的块是我故意写错的，或者只给你读、不给你抄的源码，块前会写清楚是哪种。
> 每个 `📦` 块都自带 `main`，别把两块摞进同一个文件，那样会报 `E0428`（名字重复）。换块的时候，把上一块整个替换掉。

---

## Rust 里没有 null

1965 年，托尼·霍尔（Tony Hoare）引入了空引用（null reference），后来他自己称这是「价值十亿美元的错误」。在 Java、C++、JavaScript 里，一个引用变量默认就可能是 null。你调用一个看着正常的方法，运行时突然抛出 `NullPointerException`，编译器全程不提醒你。

Rust 的做法是根本不提供 null 这个值。那「一个函数可能查不到值」这种情况怎么表达？用标准库预置的枚举 `Option<T>`。

## `Option<T>` 长什么样

它在标准库里的定义就是一个枚举：

⚠️ 这是标准库的源码，贴出来只给你读，**别抄进自己的 `main.rs`**（自己写一遍 `enum Option<T>` 会撞上什么，Day17 的踩坑框里写清楚了）：

```rust
enum Option<T> {
    Some(T), // 有值：里面包着一个 T
    None,    // 没值
}
```

`<T>` 是**泛型参数**，意思是「这里面装的类型还没定」。`Some(5)` 的时候 `T` 就是 `i32`，`Some("hi")` 的时候 `T` 就是 `&str`。泛型 Day26 专门讲，今天只要会读这个尖括号就行。

`Some` 和 `None` 太常用了，标准库把它们放进了 **prelude**（自动引入作用域的那一批名字），所以不用写 `Option::Some`：

📦 本块能单独跑：

```rust
fn main() {
    let some_number = Some(5);           // T 被推断成 i32
    let some_string = Some("一段文字");   // T 被推断成 &str

    // 单独一个 None 必须标注类型：编译器没有线索推断它是哪种 Option
    let absent_number: Option<i32> = None;

    println!("{:?} {:?} {:?}", some_number, some_string, absent_number);
}
```

为什么 `Some(5)` 不用标类型，`None` 就必须标？因为 `5` 已经把 `T` 定死成 `i32` 了，而 `None` 身上什么信息都没带。

### ⚠️ 踩坑：`let x = None;` 少写类型标注，报 `E0282`

这个坑我踩过。写 `let x = None;` 的时候觉得挺自然，编译器直接把我拦下来了。

⚠️ 本块编译不过，贴出来是给你看编译器怎么拦：

```rust
fn main() {
    let x = None;
    println!("{:?}", x);
}
```

编译器原话：

```
error[E0282]: type annotations needed for `Option<_>`
 --> src\main.rs:2:9
  |
2 |     let x = None;
  |         ^   ---- type must be known at this point
  |
help: consider giving `x` an explicit type, where the type for type parameter `T` is specified
  |
2 |     let x: Option<T> = None;
  |          +++++++++++
```

`Option<_>` 里的下划线表示「这里有个类型我推不出来」。教训很简单：只写 `None` 的时候，类型得由你补上，写成 `let x: Option<i32> = None;`。看到 `E0282` 和 `type annotations needed`，先找那个没标类型的 `let`。

## 为什么它能挡住空指针

`Option<i32>` 和 `i32` 是**两种不同的类型**，编译器不允许你把前者当后者用。下面这行在别的语言里天经地义，在 Rust 里直接编不过：

⚠️ 本块编译不过，贴出来是给你看编译器怎么拦：

```rust
fn main() {
    let x: i32 = 5;
    let y: Option<i32> = Some(5);

    let sum = x + y; // ❌ i32 和 Option<i32> 不能直接相加
    println!("{}", sum);
}
```

编译器原话：

```
error[E0277]: cannot add `Option<i32>` to `i32`
 --> src\main.rs:5:17
  |
5 |     let sum = x + y;
  |                 ^ no implementation for `i32 + Option<i32>`
  |
  = help: the trait `Add<Option<i32>>` is not implemented for `i32`
```

这行代码过不去是好事。它逼你在编译期就把「万一 `y` 是 `None` 怎么办」想清楚：你在拿到里面那个真正的 `i32` 之前，必须先把 `Option<i32>` 拆开、把 `None` 的情况处理掉。编译器不会让你带着「可能是空」的变量往下走。

## 怎么取出里面的值

那到底怎么把 `Some(5)` 里的 `5` 拿出来？有三种现成的办法，今天先认个脸熟。

### 1. `is_some()` / `is_none()` 先判断

这两个是 `Option` 自带的**方法**（Day16 讲过方法，`x.is_some()` 就是「对 `x` 做点事」），返回 `bool`：

📦 本块能单独跑：

```rust
fn main() {
    let x: Option<i32> = Some(5);

    if x.is_some() {
        println!("有值");
    }
    if x.is_none() {
        println!("没值");
    }
}
```

### 2. `unwrap()`：有值就取出，没值就 panic

📦 本块能单独跑：

```rust
fn main() {
    let x: Option<i32> = Some(5);
    let y = x.unwrap();     // 有值，直接取出 5
    println!("{}", y);

    let n: Option<i32> = None;
    println!("n 里没有值，它是 {:?}", n);
    // n.unwrap();          // ⚠️ 把这行的注释去掉，运行时会 panic
    println!("程序正常结束");
}
```

`unwrap` 的意思是「我赌它一定有值，错了你就让程序崩」。删掉注释再看一眼，运行时原话是：

```
called `Option::unwrap()` on a `None` value
```

写原型、测试时用用没关系，正式代码里随便写就等于把空指针异常请了回来。

### 3. `unwrap_or()`：没值时给个默认值

📦 本块能单独跑：

```rust
fn main() {
    let x: Option<i32> = Some(5);
    let y: Option<i32> = None;

    let a = x.unwrap_or(0);   // 有值，用里面的 5
    let b = y.unwrap_or(0);   // 没值，用默认值 0
    println!("{} {}", a, b);
}
```

「查不到就用默认值」的场景用这个最顺手。

> 按不同情况做不同事的 `match` 拆法在 Day19，`if let` 在 Day20。今天先把「`Option<T>` 和 `T` 是不同类型、必须先拆开才能用」这件事记住。

## 标准库里到处都是 Option

Rust 标准库大量用 `Option` 表示「可能查不到」：

📦 本块能单独跑：

```rust
fn main() {
    let v = vec![10, 20, 30];

    // Vec::get 按下标取元素：越界不 panic，返回 None
    // 这里先看个形状：命中 Some(val) 走第一支，命中 None 走第二支。match 是 Day19 的主角
    match v.get(10) {
        Some(val) => println!("第 10 个元素是 {}", val),
        None => println!("下标越界了"),      // 走这里
    }

    let s = "hello";
    let found = s.find('h');
    let not_found = s.find('z');
    println!("{:?} {:?}", found, not_found);   // Some(0) None
}
```

`vec![10, 20, 30]` 是标准库的宏，用来直接造一个 `Vec`（动态数组），它 Day22 才细讲，这里先照着用。`v.get(i)` 返回的类型是 `Option<&T>`，也就是借出来的引用，所以 `val` 是个 `&i32`，打印不受影响。

对比一下取值方式：`v[99]` 越界会直接 **panic 崩掉**，`v.get(99)` 安全地返回 `None`，之后怎么办你说了算。

## 一张表看懂

| 你想表达 | 别的语言 | Rust |
|---|---|---|
| 一定有个 `i32` | `int x` | `let x: i32` |
| 可能有、可能没有 | `Integer x`（可能是 null） | `let x: Option<i32>` |
| 判断有没有 | `if (x != null)` | `x.is_some()` / `x.is_none()` |
| 取值，没有就用默认 | 容易忘判空，运行时报错 | `x.unwrap_or(默认值)` |

## 今日小结

- Rust 没有 `null`，用标准库枚举 `Option<T>` 表示「可能有值、可能没值」
- `Some(T)` 包着值，`None` 表示没值；单独写 `None` 必须标注类型，不然报 `E0282`
- `Option<T>` 和 `T` 是不同类型，不能当 `T` 用，这逼你在编译期处理掉「可能是空」的情况
- 取出值：`is_some()` 判断、`unwrap()`（没值会 panic）、`unwrap_or(默认值)`
- `Vec::get`、`str::find` 这类可能失败的取法都返回 `Option`，越界和找不到不再直接崩

## 动手任务

**1.** 写一个函数 `fn first_char(s: &str) -> Option<char>`：字符串非空时返回第一个字符，空串返回 `None`。

**2.** 在 `main` 里调用它，分别传 `"hello"` 和 `""`，用 `unwrap_or('?')` 取出值打印出来（空串那次应该打印 `?`）。

**3.** 单独建一个文件，`main` 里就两行：`let n = None;` 和 `println!("{:?}", n);`。编译一下，看它报的是不是上面踩坑框里那个 `E0282`；然后给 `n` 加上 `: Option<i32>` 再编一次。

> 提示 1：取字符串第一个字符可以用 `s.chars().next()`，它本身返回的就是 `Option<char>`，正好能直接当返回值。
> 提示 2：`'?'` 是字符字面量，用单引号；`"?"` 是字符串，别写混。

**配合阅读**：[官方书·第6章 Option 枚举](https://rustwiki.org/zh-CN/book/ch06-01-defining-an-enum.html)
