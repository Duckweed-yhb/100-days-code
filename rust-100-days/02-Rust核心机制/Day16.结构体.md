# Day16 结构体 struct

> **前置知识**：Day11 引用与借用、Day13 字符串初探（`String`）
> **难度**：入门
> **预计阅读时间**：20 分钟
> **代码说明**：标 `📦` 的块是**一个完整程序**，整块贴进 `src/main.rs` 就能 `cargo run`。
> 标 `🧩` 的块是片段，要和它上面写着「接第 N 块」的那个块放同一个文件里，`fn main` 只留一个（用新的那个）。
> 因为好几个块里都定义了 `User` 或 `Rectangle`，别把两个块摞进同一个文件，那样会报 `E0428`（名字重复）。换块的时候，把上一块整个替换掉。

---

## 为什么需要结构体

到目前为止，用过的类型只有基本类型（`i32`、`f64`、`bool`）、元组和数组。可真实世界里一个东西往往由好几项属性组成：一个「用户」有用户名、邮箱、是否活跃、登录次数。用四个散变量表示也能写，但它们之间没有任何绑定关系，传参时得一股脑塞四个，顺序传错了编译器也不管。

**结构体（struct）** 把这些相关字段打包成一个有名字的自定义类型。可以把它当一张登记表的表头：每个实例按表头填内容，之后按字段名取，不靠位置。

## 定义、实例化、改字段

用 `struct` 关键字定义，字段写在大括号里：

📦 本块能单独跑：

```rust
#[derive(Debug)]
struct User {
    active: bool,        // 是否活跃
    username: String,    // 用户名
    email: String,       // 邮箱
    sign_in_count: u64,  // 累计登录次数
}

fn main() {
    // 实例化：每个字段都要赋值，顺序不要求和定义一致
    let mut user1 = User {
        email: String::from("someone@example.com"),
        username: String::from("someusername123"),
        active: true,
        sign_in_count: 1,
    };

    // 用 . 访问字段
    println!("用户 {} 的邮箱是 {}", user1.username, user1.email);
    println!("{} 活跃吗？{}，累计登录 {} 次", user1.username, user1.active, user1.sign_in_count);

    // 改字段：实例本身必须是 mut
    user1.email = String::from("another@example.com");
    println!("改过之后：{:?}", user1);
}
```

几个符号第一次出现，先交代清楚：

- `String::from("...")` 里的 `::` 表示「从某个类型里取出属于它的东西」。后面你还会看到 `Message::Quit`、`Vec::new()`，都是这个符号。
- `user1.username` 里的 `.` 是取字段，按名字取，跟定义时的顺序无关。
- `#[derive(Debug)]` 这一行叫属性（attribute）。`Debug` 是标准库里的一个 trait，`derive` 让编译器自动替你生成「调试打印」的实现。
- `{:?}` 是调试打印的占位符，`?` 就是「用 `Debug` 这套格式打」。想要多行带缩进，写 `{:#?}`。

要改字段，整个实例必须是 `mut`，Rust 不允许只把某一个字段标成可变。

## 打印整个结构体

结构体默认不能用 `{}` 打印，因为 `{}` 要求类型自己实现 `Display`，结构体没有。写成 `println!("{}", rect);` 会报 `E0277`（`` `Rectangle` doesn't implement `std::fmt::Display` ``），加回 `?` 就好。

📦 本块能单独跑：

```rust
#[derive(Debug)]     // 有了它，{:?} 才有东西可用
struct Rectangle {
    width: u32,
    height: u32,
}

fn main() {
    let rect = Rectangle { width: 30, height: 50 };

    println!("普通打印 {:?}", rect);     // 所有字段挤在一行
    println!("美化打印 {:#?}", rect);    // 每个字段一行，带缩进
    println!("宽 {} 高 {}，面积 {}", rect.width, rect.height, rect.width * rect.height);

    // dbg! 把「文件:行号 = 值」打到标准错误流，并且把值本身还给你
    let double_width = dbg!(rect.width * 2);
    println!("宽的两倍是 {}", double_width);
}
```

> `dbg!` 和 `println!` 的区别值得记一下：`dbg!` 会打印文件名和行号，不拿走所有权，还返回它包着的那个值，所以能直接写在 `let` 右边。调试时比 `println!` 顺手。

## 字段初始化简写

写构造函数时，如果参数名和字段名一模一样，可以省略 `字段名:` 这一半：

🧩 本块接上面第 1 块（`User` 已经定义过，这块没再写一遍）：

```rust
fn build_user(email: String, username: String) -> User {
    User {
        email,      // 等价于 email: email
        username,   // 等价于 username: username
        active: true,
        sign_in_count: 1,
    }
}

fn main() {
    let u = build_user(String::from("a@b.com"), String::from("alice"));
    println!("{} 的邮箱是 {}，active={}，登录 {} 次", u.username, u.email, u.active, u.sign_in_count);
}
```

把第 1 块的结构体定义留在文件里，`fn main` 换成这块的，就能跑。这是日常写构造函数的惯用写法，能少写很多重复。

## 结构体更新语法 `..`

想基于旧实例造一个新实例，只改一两个字段，其余照搬，用 `..旧实例`：

🧩 本块接上面第 1 块（`User` 已经定义过）：

```rust
fn main() {
    let user1 = User {
        email: String::from("someone@example.com"),
        username: String::from("someusername123"),
        active: true,
        sign_in_count: 1,
    };

    let user2 = User {
        email: String::from("another@example.com"),   // 只改邮箱
        ..user1                                       // 剩下的字段从 user1 拿
    };

    println!("user2 的邮箱 {}", user2.email);
    println!("user2 的用户名 {}", user2.username);

    // user1.username 和 user1.email 已经被移走，不能再用；
    // 但 bool、u64 实现了 Copy，这两个字段还在 user1 身上
    println!("user1 还剩 active={} sign_in_count={}", user1.active, user1.sign_in_count);
}
```

两点要注意：

- `..user1` 必须放在最后。
- `bool`、`u64` 这类实现了 `Copy` 的字段相当于拷了一份，`user1.active` 照样能读；`String` 没有实现 `Copy`，`..user1` 会把 `username`、`email` **移动**到 `user2`，之后 `user1.username` 就不能用了。这种情况也别再把 `user1` 整个塞进 `{:?}`，它已经被部分移走了。

## 三种结构体

除了上面这种「命名字段结构体」，Rust 还有两种。

### 元组结构体（tuple struct）

字段没有名字，整体有个类型名，用 `.0`、`.1`、`.2` 访问。适合你只想要一个区别于普通元组的新类型、但不关心字段名的场景：

📦 本块能单独跑：

```rust
struct Color(i32, i32, i32);  // RGB 颜色
struct Point(i32, i32, i32);  // 三维坐标

fn main() {
    let black = Color(0, 0, 0);
    let origin = Point(0, 0, 0);

    println!("R = {}", black.0);    // 0
    println!("G = {}，B = {}", black.1, black.2);

    println!("x = {}", origin.0);   // 0
    println!("y = {}，z = {}", origin.1, origin.2);
}
```

`Color` 和 `Point` 内部都是三个 `i32`，但它们是**不同的类型**，不能互相赋值，写成 `let origin = black;` 编译器会拦。类型名在这里就是用来区分「这是颜色」还是「这是坐标」的。

### 类单元结构体（unit-like struct）

一个字段都没有，长得像一个空结构体：

📦 本块能单独跑：

```rust
struct AlwaysEqual;

fn main() {
    let _subject = AlwaysEqual;   // 变量名前加 _ 表示「我暂时不用它」，编译器就不发未使用警告
    println!("类单元结构体的值造出来了");
}
```

它适合「我只需要一个类型、不需要它存数据」的场景，比如后面要为某个类型实现一个 trait。今天先混个脸熟。

## 方法：给结构体挂行为

光有数据不够，我们常需要「对这个数据做的操作」。Rust 用 `impl`（implementation 的缩写）块给结构体定义**方法（method）**：

📦 本块能单独跑：

```rust
#[derive(Debug)]
struct Rectangle {
    width: u32,
    height: u32,
}

impl Rectangle {
    // 第一个参数是 &self，代表调用这个方法的实例本身
    fn area(&self) -> u32 {
        self.width * self.height
    }

    // self 之后就是普通参数
    fn can_hold(&self, other: &Rectangle) -> bool {
        self.width > other.width && self.height > other.height
    }
}

fn main() {
    let rect1 = Rectangle { width: 30, height: 50 };
    let rect2 = Rectangle { width: 10, height: 40 };
    let rect3 = Rectangle { width: 60, height: 45 };

    println!("rect1 的面积是 {}", rect1.area());                     // 1500
    println!("rect1 能放下 rect2 吗？{}", rect1.can_hold(&rect2));    // true
    println!("rect1 能放下 rect3 吗？{}", rect1.can_hold(&rect3));    // false

    // can_hold 只是借了 rect2 看一眼，所以它还在
    println!("rect2 依然可用：{:?}", rect2);
}
```

`&self` 里的 `&` 就是 Day11 学的引用：方法只是借实例来看，不把它拿走，所以 `rect2` 传给 `can_hold` 之后还能继续用。`self.width` 就是 `rect1.width`，`self` 指「调用我那个实例」。

关于 `self` 的三种形式：

| 写法 | 含义 | 用途 |
|---|---|---|
| `&self` | 不可变借用实例 | 只读操作，最常用 |
| `&mut self` | 可变借用实例 | 要修改实例字段 |
| `self` | 拿走实例所有权 | 很少用，通常是消费掉实例 |

调用方法直接写 `rect1.area()`，Rust 会自动帮你加引用或解引用，没有 C++ 里那个 `->` 运算符。

## 关联函数：`::new`

`impl` 块里如果第一个参数**不是** `self`，那它不是方法，而是**关联函数（associated function）**，属于类型本身，常用作构造函数，调用时写 `类型名::函数名()`：

🧩 本块接上面第 7 块（`Rectangle` 和 `area` 方法已经定义过）：

```rust
impl Rectangle {
    // 没有 self 参数，就是关联函数，这里用来造一个正方形
    fn square(size: u32) -> Rectangle {
        Rectangle { width: size, height: size }
    }
}

fn main() {
    let sq = Rectangle::square(3);   // 注意是 :: 不是 .
    println!("正方形面积 {}", sq.area());
}
```

`impl` 块可以写很多个，所以这块直接加在第 7 块的 `impl Rectangle` 后面就行，`fn main` 换成这块的。换完 `main` 之后，第 7 块里的 `can_hold` 暂时没人调用，编译器会发一条 `never used` 警告，那是提醒不是错误，不用管它。

你早就见过关联函数了：`String::new()`、`Vec::new()` 都是。

## 方法可以和字段同名

Rust 允许方法和字段取一样的名字。比如给 `Rectangle` 写一个方法叫 `width`，那么不加括号访问的是字段，加了括号调用的是方法。这个技巧在封装和 getter 里会用到，现在了解即可。

## 今日小结

- `struct` 把相关字段打包成自定义类型，按名访问；改字段要整个实例 `mut`
- `#[derive(Debug)]` 加上 `{:?}` / `{:#?}` 是打印结构体的标配，`{}` 用不了
- 字段简写（`email` 代替 `email: email`）和 `..old` 更新语法能少写重复；`..old` 会移动没有实现 `Copy` 的字段
- 元组结构体（`Color(i32, i32, i32)`）和类单元结构体（`struct Empty;`）是两种轻量变体
- `impl` 块里带 `&self` 的是方法，没有 `self` 的是关联函数，用 `类型名::函数名()` 调用

## 动手任务

**1.** 定义一个 `Student` 结构体，包含 `name: String`、`score: u32`、`passed: bool`，再写一个构造函数 `build_student`（用字段简写）。打印出来看看。

**2.** 在第 7 块那个文件上，给 `Rectangle` 加一个方法 `fn enlarge(&mut self, factor: u32)`，把宽和高都乘以 `factor`，在 `main` 里调用后打印 `{:?}`，确认实例真的变了。

**3.** 先合上第 8 块，在第 7 块那个文件上自己写一个关联函数 `fn square(size: u32) -> Rectangle`，用它造一个正方形，打印它的面积。写完再对一眼第 8 块。

> 提示：用到 `&mut self` 时，实例本身要声明成 `let mut rect = ...`，少写 `mut` 会报 `E0596`。

**配合阅读**：[官方书·第5章 定义并实例化结构体](https://rustwiki.org/zh-CN/book/ch05-01-defining-structs.html)
