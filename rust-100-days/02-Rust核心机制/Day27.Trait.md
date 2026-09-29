# Day27 Trait

> 目标：学会用 `trait` 定义「一组行为契约」，让不同类型共享同一套方法，并能用 trait bound 写泛型函数。

## Trait 是什么

如果说结构体/枚举描述「一个数据长什么样」，那 **trait** 就描述「一个类型能做什么」。它和其他语言里的**接口（interface）**很像：定义一组方法签名，实现了这个 trait 的类型就保证拥有这些方法。

举个例子：不管是新闻文章还是一条推文，都有个「摘要」。我们可以定义一个 `Summary` trait，规定「任何会摘要的东西都得有 `summarize` 方法」。

## 定义与实现 trait

```rust
// 定义 trait：规定实现者必须有 summarize 方法
pub trait Summary {
    fn summarize(&self) -> String;
}

pub struct NewsArticle {
    pub headline: String,
    pub author: String,
}

// 为 NewsArticle 实现 Summary
impl Summary for NewsArticle {
    fn summarize(&self) -> String {
        format!("《{}》- {}", self.headline, self.author)
    }
}

pub struct Tweet {
    pub username: String,
    pub content: String,
}

impl Summary for Tweet {
    fn summarize(&self) -> String {
        format!("{}: {}", self.username, self.content)
    }
}
```

注意写法：`impl Summary for 类型`——「为某个类型实现某个 trait」。不同类型可以用完全不同的逻辑实现同一个方法名，这就是多态的基础。

## 孤儿规则

有一条铁律叫**孤儿规则（orphan rule）**：只有当 trait 或类型至少有一个是你当前 crate 本地定义的，你才能为它实现该 trait。换句话说，你**不能**给外部类型实现外部 trait——比如不能给标准库的 `Vec<T>` 实现一个你自己在网上抄来的 trait。

为什么？因为如果两个 crate 都给 `Vec` 实现了同一个 trait，编译器就懵了，不知道该用哪个。这条规则保证 trait 实现不会打架。

## 默认实现

trait 里的方法可以直接给个默认实现，实现者能用现成的，也可以覆盖：

```rust
pub trait Summary {
    fn summarize_author(&self) -> String; // 必须自己实现

    fn summarize(&self) -> String {       // 有默认实现
        format!("（阅读全文，作者 {} ...）", self.summarize_author())
    }
}

impl Summary for Tweet {
    fn summarize_author(&self) -> String {
        format!("@{}", self.username)
    }
    // 不覆盖 summarize，直接用默认实现
}
```

## 把 trait 当参数

你想写个函数，它能接收「任何会摘要的东西」并打印摘要。用 `impl Trait` 语法：

```rust
pub fn notify(item: impl Summary) {
    println!("快讯：{}", item.summarize());
}
```

`item: impl Summary` 的意思是：item 可以是任何实现了 `Summary` 的类型。不管是 `NewsArticle` 还是 `Tweet` 都能传进来。

这等价于更正式的 **trait bound** 写法：

```rust
pub fn notify<T: Summary>(item: T) {
    println!("快讯：{}", item.summarize());
}
```

什么时候用哪种？当需要强制多个参数是**同一个类型**时，必须用 trait bound：

```rust
// 两个参数可以是不同类型（只要都实现 Summary）
pub fn notify1(a: impl Summary, b: impl Summary) {}

// 两个参数必须是同一个类型 T
pub fn notify2<T: Summary>(a: T, b: T) {}
```

## 多个 trait bound 用 `+`

要求一个类型同时实现多个 trait，用 `+`：

```rust
use std::fmt::Display;

// item 必须同时实现 Summary 和 Display
pub fn notify(item: impl Summary + Display) {}
```

约束一多，函数签名就很长。用 `where` 子句把约束抽出来，清爽很多：

```rust
fn some_function<T, U>(t: T, u: U) -> i32
where
    T: Display + Clone,
    U: Clone + std::fmt::Debug,
{
    0
}
```

## 返回实现了 trait 的类型

函数返回值也能写 `impl Trait`，表示「返回某个实现了该 trait 的类型」，但不暴露具体是哪个：

```rust
fn returns_summarizable() -> impl Summary {
    Tweet {
        username: String::from("horse_ebooks"),
        content: String::from("你好"),
    }
}
```

> 限制：只能返回**单一具体类型**。不能根据条件有时返回 `Tweet`、有时返回 `NewsArticle`。要做真正的多态返回（trait object）是后面 Day41 的内容。

## derive：编译器帮你实现 trait

你已经见过 `#[derive(Debug)]` 了。像 `Debug`、`Clone`、`PartialEq`、`Copy` 这些常用 trait，编译器能自动帮你实现，不用手写：

```rust
#[derive(Debug, Clone, PartialEq)]
struct Point {
    x: i32,
    y: i32,
}
```

## 今日小结

- trait 定义一组行为契约（类似接口），用 `impl Trait for 类型` 为具体类型实现
- 孤儿规则：trait 或类型至少有一个是本地的，才能实现该 trait
- trait 方法可以有默认实现，实现者可直接用或覆盖
- `fn f(item: impl Trait)` 接收任何实现了该 trait 的类型；等价写法 `fn f<T: Trait>(item: T)`
- 多约束用 `+`，复杂签名用 `where` 子句；`impl Trait` 可作返回类型（只能单类型）

## 动手任务

1. 定义 trait `Draw { fn draw(&self) -> String; }`，为 `Circle` 和 `Square` 两个结构体分别实现它。
2. 写函数 `fn render(shape: impl Draw)`，调用 `shape.draw()` 并打印，分别传入 Circle 和 Square 验证多态。
3. 给你的结构体加 `#[derive(Debug, Clone)]`，试试能不能用 `{:?}` 打印、能不能 `.clone()`。

> 提示：`impl Trait for 类型` 里的方法第一个参数通常是 `&self`。

**配合阅读**：[官方书·第10章 trait：定义共享的行为](https://rustwiki.org/zh-CN/book/ch10-02-traits.html)
