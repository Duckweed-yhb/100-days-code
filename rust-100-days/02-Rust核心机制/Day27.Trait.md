# Day27 Trait

> **前置知识**：Day16 结构体、Day26 泛型
> **难度**：进阶
> **预计阅读时间**：25 分钟
> **代码说明**：本章代码都放在**同一个** `main.rs` 里。标了 🧩 的块是接着上面改的，一个文件里同名类型不能定义两次，所以那是替换，不是叠加。
> 每块前面标了 `📦`（能单独跑）或 `🧩`（依赖上面的块，别单拎出来）。

目标：学会用 `trait` 定义一组行为契约，让不同类型共享同一套方法，并用 trait bound 写泛型函数。

---

## Trait 是什么

结构体和枚举描述一个数据长什么样，**trait** 描述一个类型能做什么。它和别的语言里的**接口（interface）**是一回事：里面列出一组方法签名，谁实现了这个 trait，谁就保证有这些方法。

拿摘要举例。新闻文章和推文都能给出一份摘要，那就定义一个 `Summary` trait，规定「会摘要的东西必须有 `summarize` 方法」。

---

## 定义与实现 trait

📦 本块能单独跑：

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

fn main() {
    let article = NewsArticle {
        headline: String::from("头条"),
        author: String::from("老王"),
    };
    let tweet = Tweet {
        username: String::from("horse_ebooks"),
        content: String::from("今天风向不错"),
    };

    println!("{}", article.summarize());
    println!("{}", tweet.summarize());
}
```

写法是 `impl Summary for 类型`，读作「为某个类型实现某个 trait」。两个类型用完全不同的逻辑实现了同名方法，多态就是从这里开始的。

几个新写法解释一下：

- `summarize(&self)` 里的 `&self` 指的是「调用这个方法的那个值」。`&` 表示借用，方法只读不改。Day11 学的借用通常写在参数上，这里是 `&self`，换了个位置而已。
- `format!` 和 `println!` 一样是宏，它把结果拼成一个 `String` 返回，而不是打印出来。
- `String` 字段和 `String::from` 都是 Day23 的内容。

---

## 孤儿规则

有一条铁律叫**孤儿规则（orphan rule）**：只有当 trait 或类型至少有一个是你当前 crate 本地定义的，你才能为它实现该 trait。换句话说，你不能给外部类型实现外部 trait。比如给标准库的 `Vec<T>` 实现一个从网上抄来的 trait，编译器会拒绝。

为什么要这样？假如两个 crate 都给 `Vec` 实现了同一个 trait，编译器就不知道调用时该用哪一份实现。这条规则保证 trait 实现不会打架。

crate 是 Day21 讲过的概念，一个包（package）大致对应一个可执行 crate 或一个库 crate。

---

## 默认实现

trait 里的方法可以直接给个默认实现，实现者能用现成的，也可以覆盖：

🧩 本块接上面第 1 块（把 `Summary` 换成这个带默认实现的版本，两个类型都补上 `summarize_author`；块里的 `main` 替换掉上一块的 `main`）：

```rust
pub trait Summary {
    fn summarize_author(&self) -> String; // 必须自己实现

    fn summarize(&self) -> String {       // 有默认实现
        format!("（阅读全文，作者 {} ...）", self.summarize_author())
    }
}

impl Summary for NewsArticle {
    fn summarize_author(&self) -> String {
        self.author.clone()
    }
}

impl Summary for Tweet {
    fn summarize_author(&self) -> String {
        format!("@{}", self.username)
    }
    // 不覆盖 summarize，直接用默认实现
}

fn main() {
    let tweet = Tweet {
        username: String::from("horse_ebooks"),
        content: String::from("今天风向不错"),
    };
    println!("{}", tweet.summarize());
}
```

`summarize_author` 没有函数体，只有签名，所以实现者必须自己写。`summarize` 有函数体，实现者不写也能用。少写任何一个该写的方法，这份 `impl` 就编不过。

默认实现里还能调用同一个 trait 里别的方法，上面 `summarize` 就调了 `summarize_author`。这是设计上的好处：实现者只补最关键的一个方法，其余流程由 trait 提供。

---

## 把 trait 当参数

想写个函数，接收「任何会摘要的东西」并打印摘要，用 `impl Trait` 语法：

🧩 本块接上面第 2 块（`Summary` 和 `Tweet` 都在，这段加进同一个文件）：

```rust
fn notify(item: impl Summary) {
    println!("快讯：{}", item.summarize());
}

fn main() {
    let tweet = Tweet {
        username: String::from("horse_ebooks"),
        content: String::from("今天风向不错"),
    };
    notify(tweet); // Tweet 实现了 Summary，能传进来
}
```

`item: impl Summary` 读作「item 是某个实现了 `Summary` 的类型」。`NewsArticle` 和 `Tweet` 都能传进来，函数内部不关心具体是哪一个。

它等价于更正式的 **trait bound** 写法：

🧩 本块接上面第 3 块（把 `notify` 换成这个写法，两个 `notify` 只能留一个）：

```rust
fn notify<T: Summary>(item: T) {
    println!("快讯：{}", item.summarize());
}
```

`<T: Summary>` 就是 Day26 那个尖括号加约束的写法，只是约束换成了 trait。

两种写法有实际区别。需要强制多个参数是**同一个类型**时，只能用 trait bound：

🧩 本块接上面第 4 块（这里只示范函数签名，两个函数都别真的去调用）：

```rust
// 两个参数可以是不同类型（只要都实现 Summary）
fn notify1(a: impl Summary, b: impl Summary) {}

// 两个参数必须是同一个类型 T
fn notify2<T: Summary>(a: T, b: T) {}
```

---

## 多个 trait bound 用 `+`

要求一个类型同时实现多个 trait，用 `+` 连起来：

🧩 本块接上面第 5 块（`Display` 是标准库的 trait，`use` 一下就能用）：

```rust
use std::fmt::Display;

// item 必须同时实现 Summary 和 Display
fn notify3(item: impl Summary + Display) {}
```

`Display` 决定一个类型用 `{}` 打印时输出什么。你给 `Circle` 实现了 `Draw` 还不够，想 `println!("{}", shape)` 就得另外实现 `Display`。

约束一多，函数签名会拖得很长。用 `where` 子句把约束挪到后面，读起来清爽：

🧩 本块接上面第 6 块（同一个文件里留着这个函数就行）：

```rust
use std::fmt::Display;

fn some_function<T, U>(t: T, u: U) -> i32
where
    T: Display + Clone,
    U: Clone + std::fmt::Debug,
{
    0
}
```

`where` 后面每行写一个类型参数和它的约束，多条约束之间还是用 `+`。上面这个函数没有任何实际逻辑，只看签名就行。

---

## 返回实现了 trait 的类型

函数返回值也能写 `impl Trait`，表示「返回某个实现了该 trait 的类型」，但不告诉你具体是哪个：

🧩 本块接上面第 2 块（`Tweet` 和 `Summary` 来自上面）：

```rust
fn returns_summarizable() -> impl Summary {
    Tweet {
        username: String::from("horse_ebooks"),
        content: String::from("今天风向不错"),
    }
}
```

限制是只能返回**单一具体类型**。不能今天返回 `Tweet`、明天返回 `NewsArticle`，哪怕两个都实现了 `Summary`。要做真正的多态返回得用 trait object，那是 Day41 的内容。

---

## derive：编译器帮你实现 trait

`#[derive(...)]` 让编译器自动生成 trait 的实现，你一个方法都不用写。像 `Debug`、`Clone`、`PartialEq`、`Copy` 这些常用 trait 都支持：

📦 本块能单独跑：

```rust
#[derive(Debug, Clone, PartialEq)]
struct Point {
    x: i32,
    y: i32,
}

fn main() {
    let p = Point { x: 1, y: 2 };
    let q = p.clone(); // Clone 给的复制方法

    println!("{:?}", q);              // Debug 给的调试打印
    println!("p == q 是 {}", p == q); // PartialEq 给的 ==
}
```

`#[derive(Debug)]` 在 Day17 用过，`{:?}` 就是它带来的。`Clone` 提供 `.clone()`，`PartialEq` 提供 `==`。有前提：只有所有字段都实现了某个 trait，`derive` 才能替这个结构体实现它。字段里塞一个没实现 `Clone` 的自定义类型，`#[derive(Clone)]` 就会报错。

---

## 今日小结

- trait 定义一组行为契约（类似接口），用 `impl Trait for 类型` 为具体类型实现
- 孤儿规则：trait 或类型至少有一个是本地的，才能实现该 trait
- trait 方法可以有默认实现，实现者直接用或覆盖
- `fn f(item: impl Trait)` 接收任何实现了该 trait 的类型；等价写法 `fn f<T: Trait>(item: T)`
- 多约束用 `+`，复杂签名用 `where` 子句；`impl Trait` 可作返回类型（只能单类型）
- `#[derive(...)]` 让编译器替你实现 `Debug`、`Clone`、`PartialEq` 这类常用 trait

---

## 动手任务

1. 定义 trait `Draw { fn draw(&self) -> String; }`，为 `Circle` 和 `Square` 两个结构体分别实现它。
2. 写函数 `fn render(shape: impl Draw)`，调用 `shape.draw()` 并打印，分别传入 `Circle` 和 `Square`，看同一份代码能不能处理两种形状。
3. 给你的结构体加 `#[derive(Debug, Clone)]`，试试能不能用 `{:?}` 打印、能不能 `.clone()`。

> 提示：`impl Trait for 类型` 里的方法第一个参数通常是 `&self`，方法体里用 `self.字段名` 访问字段。`render` 接收的是 `impl Draw`，所以传进去的必须是实现了 `Draw` 的类型，`Circle` 和 `Square` 都得各自写一份 `impl Draw`。

---

**配合阅读**：[官方书·第10章 trait：定义共享的行为](https://rustwiki.org/zh-CN/book/ch10-02-traits.html)
