# Day16 结构体 struct

> 目标：学会用 `struct` 把相关的数据打包成一个自定义类型，并能用 `impl` 给它挂方法，像写面向对象那样组织自己的代码。

## 为什么需要结构体

到目前为止，我们用过的类型都是基本类型（`i32`、`f64`、`bool`）和元组、数组。但真实世界里一个东西往往由好几个属性组成：比如一个「用户」有用户名、邮箱、是否活跃、登录次数。你当然可以用四个散乱的变量表示，但它们之间没有绑定关系，传参时也得一股脑塞四个，很容易传错顺序。

**结构体（struct）**就是把这些相关字段打包成一个有名字的自定义类型。你可以把它理解成一张「登记表的表头」：每一行（实例）都按表头填各项内容，按名字访问，而不是靠位置。

## 定义与实例化

用 `struct` 关键字定义，字段写在大括号里：

```rust
// #[derive(Debug)] 表示允许用 {:?} 打印调试信息，后面会讲
#[derive(Debug)]
struct User {
    active: bool,        // 是否活跃
    username: String,    // 用户名
    email: String,       // 邮箱
    sign_in_count: u64,  // 累计登录次数
}

fn main() {
    // 实例化：每个字段都要赋值，顺序不要求和定义一致
    let user1 = User {
        email: String::from("someone@example.com"),
        username: String::from("someusername123"),
        active: true,
        sign_in_count: 1,
    };

    // 用 . 访问字段
    println!("用户 {} 的邮箱是 {}", user1.username, user1.email);
}
```

几个新手必踩的点：

- **要修改字段，整个实例必须是 `mut`**：Rust 不允许只把某一个字段标记为可变。
- **结构体默认不能用 `{}` 打印**：必须在定义上加 `#[derive(Debug)]`，然后用 `{:?}` 或 `{:#?}`（美化换行）来打印。

```rust
#[derive(Debug)]
struct Rectangle {
    width: u32,
    height: u32,
}

fn main() {
    let rect = Rectangle { width: 30, height: 50 };
    println!("普通打印 {:?}", rect);        // 一行
    println!("美化打印 {:#?}", rect);       // 每个字段一行
}
```

运行下面命令看看输出：

```powershell
cargo run
```

> 还有个很方便的调试宏 `dbg!()`：它会把文件、行号和表达式的值打印到 stderr，并且**返回值本身**（不拿走所有权）。例如 `let w = dbg!(rect.width * 2);`。

## 字段初始化简写

写构造函数时，如果参数名和字段名一模一样，可以省略 `字段名: 参数名`：

```rust
fn build_user(email: String, username: String) -> User {
    User {
        email,      // 等价于 email: email
        username,   // 等价于 username: username
        active: true,
        sign_in_count: 1,
    }
}
```

这是日常写构造函数时的惯用写法，能少写很多重复。

## 结构体更新语法 `..`

想基于旧实例造一个新实例，只改其中一两个字段，其余照搬？用 `..旧实例`：

```rust
let user2 = User {
    email: String::from("another@example.com"),
    ..user1 // 剩下的字段从 user1 复制
};
```

注意两点：

- `..user1` 必须放在最后。
- 对 `bool`、`u64` 这类实现了 `Copy` 的字段就是拷一份；但 `String` 这类有所有权的字段会发生**所有权移动**，移动后 `user1` 里那些字段就不能再用了（`active`、`sign_in_count` 还能用）。

## 三种结构体

除了上面这种「命名字段结构体」，Rust 还有两种：

### 元组结构体（tuple struct）

字段没有名字，整体有个类型名，用 `.0 .1 .2` 访问。适合你只想要一个区别于普通元组的新类型，但不关心字段名的场景：

```rust
struct Color(i32, i32, i32); // RGB 颜色
struct Point(i32, i32, i32);  // 三维坐标

let black = Color(0, 0, 0);
let origin = Point(0, 0, 0);

println!("R = {}", black.0); // 0
```

虽然 `Color` 和 `Point` 的内部都是三个 `i32`，但它们是**不同的类型**，不能互相赋值。

### 类单元结构体（unit-like struct）

一个字段都没有，长得像空结构体：

```rust
struct AlwaysEqual;

let subject = AlwaysEqual;
```

它适合「我只需要一个类型、不需要它存数据」的场景，比如后面要为某个类型实现一个 trait。现在先混个脸熟。

## 方法：给结构体挂行为

光有数据还不够，我们常需要「对这个数据做的操作」。Rust 用 `impl`（implementation 的缩写）块给结构体定义**方法（method）**：

```rust
#[derive(Debug)]
struct Rectangle {
    width: u32,
    height: u32,
}

impl Rectangle {
    // 第一个参数永远是 &self，代表调用这个方法的实例本身
    fn area(&self) -> u32 {
        self.width * self.height
    }

    // 带更多参数：self 之后就是普通参数
    fn can_hold(&self, other: &Rectangle) -> bool {
        self.width > other.width && self.height > other.height
    }
}

fn main() {
    let rect1 = Rectangle { width: 30, height: 50 };
    let rect2 = Rectangle { width: 10, height: 40 };
    let rect3 = Rectangle { width: 60, height: 45 };

    println!("rect1 的面积是 {}", rect1.area());           // 1500
    println!("rect1 能放下 rect2 吗？{}", rect1.can_hold(&rect2)); // true
    println!("rect1 能放下 rect3 吗？{}", rect1.can_hold(&rect3)); // false
}
```

关于 `self` 的三种形式：

| 写法 | 含义 | 用途 |
|---|---|---|
| `&self` | 不可变借用实例 | 只读操作，最常用 |
| `&mut self` | 可变借用实例 | 要修改实例字段 |
| `self` | 拿走实例所有权 | 很少用，通常是消费掉实例 |

调用方法时直接写 `rect1.area()`，Rust 会自动帮你加引用/解引用，**没有 C++ 里那个 `->` 运算符**。

## 关联函数：`::new`

`impl` 块里如果第一个参数**不是** `self`，那它就不是方法，而是**关联函数（associated function）**——属于类型本身，常用作构造函数，调用时用 `类型名::函数名()`：

```rust
impl Rectangle {
    // 没有 self 参数 → 关联函数，常用于构造
    fn square(size: u32) -> Rectangle {
        Rectangle { width: size, height: size }
    }
}

fn main() {
    let sq = Rectangle::square(3); // 注意是 :: 不是 .
    println!("正方形面积 {}", sq.area());
}
```

你其实早就见过关联函数了：`String::new()`、`Vec::new()` 都是。

## 方法可以和字段同名

Rust 允许方法和字段取一样的名字。比如给 `Rectangle` 写一个方法叫 `width`：不加括号访问的是字段，加了括号调用的是方法。这个技巧在后面封装、getter 里会用到，现在了解即可。

## 今日小结

- 结构体 `struct` 把相关字段打包成自定义类型，按名访问；修改字段要整个实例 `mut`
- `#[derive(Debug)]` + `{:?}` / `{:#?}` 是打印结构体的标配
- 字段简写（`email` 代替 `email: email`）和 `..old` 更新语法能少写很多重复
- 元组结构体（`Color(i32,i32,i32)`）和类单元结构体（`struct Empty;`）是两种轻量变体
- `impl` 块里用 `&self` 定义方法；没有 `self` 的是关联函数，用 `类型名::函数名()` 调用

## 动手任务

1. 定义一个 `Student` 结构体，包含 `name: String`、`score: u32`、`passed: bool`，写一个 `build_student` 构造函数（用字段简写）。
2. 给 `Rectangle` 加一个方法 `enlarge(&mut self, factor: u32)`，把宽和高都乘以 `factor`，验证调用后实例真的变了。
3. 写一个关联函数 `Rectangle::square(size: u32) -> Rectangle`，并用它造一个正方形，打印面积。

> 提示：用到 `&mut self` 时，记得实例本身要声明成 `let mut rect = ...`。

**配合阅读**：[官方书·第5章 定义并实例化结构体](https://rustwiki.org/zh-CN/book/ch05-01-defining-structs.html)
