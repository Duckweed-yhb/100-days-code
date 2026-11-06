# Day17 枚举 enum

> **前置知识**：Day16 结构体（尤其类单元结构体、命名字段结构体、元组结构体）
> **难度**：入门
> **预计阅读时间**：15 分钟
> **代码说明**：本章代码都放在**同一个** `main.rs` 里，按顺序往下加就能跑。
> 每块前面标了 `📦`（能单独跑）或 `🧩`（依赖上面的块，别单拎出来）。

---

## 为什么需要枚举

结构体描述的是「一个东西同时拥有哪些字段」。但有些场景不是这样：一张消息，要么是「退出」信号，要么是「移动到坐标」，要么是「发送一段文本」，要么是「改颜色」。

这四件事不可能同时发生，但都属于「消息」。

**枚举（enumeration，enum）**就是干这个的：你先列出所有可能的情况（叫**变体 variant**），一个值在任一时刻只能是其中之一。

生活里的例子：扑克牌的花色只能是黑桃、红桃、梅花、方块四种之一；星期只能是周一到周日之一。

---

## 定义枚举

📦 本块能单独跑：

```rust
enum Message {
    Quit,                        // 变体1：不带任何数据
    Move { x: i32, y: i32 },     // 变体2：带一个匿名结构体
    Write(String),               // 变体3：带一个 String
    ChangeColor(i32, i32, i32),  // 变体4：带三个 i32
}

fn main() {
    // 用 :: 从枚举命名空间里取出变体
    let _m1 = Message::Quit;
    let _m2 = Message::Write(String::from("hello"));
    let _m3 = Message::ChangeColor(255, 0, 0);

    println!("三个消息都造出来了");
}
```

（变量名前面加 `_` 是告诉编译器「我知道我暂时不用它」，不然会有一条 unused 警告。这跟正确性无关，只是让输出干净。）

四个变体带的数据各不相同：`Quit` 什么都不带，`Move` 带两个命名字段，`Write` 带一个 `String`，`ChangeColor` 带三个 `i32`。

这些写法你在 Day16 都见过，分别对应类单元结构体、命名字段结构体、元组结构体。**枚举就是把这几种长得不一样的结构体打包成同一个类型。**

---

## 为什么这比定义四个结构体强

你可能会想：分别定义 `Quit`、`Move`、`WriteMsg`、`ChangeColor` 四个结构体不就行了？

问题出在使用的时候。四个结构体是四个**不同类型**，你没法把它们放进同一个数组、传给同一个函数参数。

🧩 本块接上面第 1 块（`Message` 已经定义过了）：

```rust
fn main() {
    // 四个变体同属 Message 类型，可以装进同一个 Vec
    let messages: Vec<Message> = vec![
        Message::Quit,
        Message::Write(String::from("hello")),
        Message::ChangeColor(0, 255, 0),
    ];
    println!("装下了 {} 条消息", messages.len());
}

fn process(_msg: Message) {
    // 今天先空着，Day19 学 match 后再来处理它
}
```

**`process` 只收一个 `Message`，不用关心具体是哪种。** 这是枚举最难被替代的地方：把「同属一类但形态不同」的数据统一成一种类型。

---

## 枚举也能挂方法

跟结构体一样，枚举也能用 `impl` 块定义方法。

🧩 接上面：

```rust
impl Message {
    fn call(&self) {
        println!("有人发了一条消息");
    }
}

fn main() {
    let m = Message::Write(String::from("hello"));
    m.call();
}
```

---

## 结构体 vs 枚举

| | 结构体 struct | 枚举 enum |
|---|---|---|
| 描述 | 一个东西**同时有**哪些字段 | 一个东西**只能是**哪几种情况之一 |
| 变体关系 | 全部字段同时存在 | 任一时刻只有一个变体生效 |
| 典型例子 | 一个用户有用户名、邮箱、年龄 | 一笔资金流向：转账 / 退款 / 冻结 |
| 能否放同一容器 | 同类型结构体可以 | 不同变体天然同属一个枚举类型 |

这张表值得多看两眼。**判断该用哪个，就问一句：这几样东西是「同时都在」，还是「只可能是其中一个」？** 同时都在用结构体，只可能是其中一个用枚举。

---

## 枚举在内存里长什么样

Rust 给每个变体分配一个整数 **discriminant（判别值）**，再加上它携带的数据。所以枚举通常很省内存。

后面操作系统训练营里你会大量用到它，比如用枚举表示「任务状态：就绪 / 运行 / 阻塞」。

默认判别值从 0 开始，也可以手动指定：

📦 本块能单独跑：

```rust
#[derive(Debug)]
enum Color {
    Red = 0xff0000,
    Green = 0x00ff00,
    Blue = 0x0000ff,
}

fn main() {
    println!("{:?}", Color::Red);
    println!("Red 的判别值 = {}", Color::Red as i32);
}
```

---

## `Option<T>` 是标准库里的枚举

标准库最常用的枚举是 `Option<T>`，表示「一个值可能存在、也可能不存在」。

**它的定义长这样（这是标准库源码，不是你该抄的代码）：**

```rust
// std 里的定义，仅供阅读 —— 不要抄进自己的项目！
pub enum Option<T> {
    Some(T), // 有值，里面包着一个 T
    None,    // 没有值
}
```

它凭什么重要、凭什么取代其他语言里的 `null`，Day18 专门讲。今天你只要知道：**变体可以携带数据，而 `Option` 就是这么个枚举。**

用法是这样（这里的 `Option` 是标准库那个，你没自己定义过）：

📦 本块能单独跑：

```rust
fn main() {
    let some_num = Some(5);              // 有值
    let absent: Option<i32> = None;      // 没值，必须标注类型

    println!("{:?} {:?}", some_num, absent);
}
```

### ⚠️ 踩坑：千万别自己写一遍 `enum Option<T>`

上面那段标准库源码，如果你照着敲进自己的 `main.rs`，会出一件很难看懂的事。

**我自己就踩过这个坑**（2026-10-12）。当时我在文件里写了 `enum Option<T>`，然后第 40 行写：

```rust
let absent: Option<i32> = None;
```

编译器的原话是：

```
error[E0308]: mismatched types
   = note: `std::option::Option<_>` and `Option<i32>` have similar names,
           but are actually distinct types
note: `std::option::Option<_>` is defined in crate `core`
note: `Option<i32>` is defined in the current crate
  --> src\main.rs:8:1
   |
 8 | enum Option<T> {
   | ^^^^^^^^^^^^^^
```

**你在当前文件里定义的名字，会盖住标准库的同名项。** 所以第 40 行的 `Option<i32>` 指的是**你自己那个** `Option`，跟 `std` 的完全是两个类型，对不上。

这件事的教训跟枚举无关，是通用的：**教程里用来「给你看」的源码，不都是「给你抄」的。** 看的时候先分清这两种。

---

## 今日小结

- `enum` 描述「一个值只能是几种固定情况之一」，变体用 `枚举名::变体名` 访问
- 每个变体可以携带不同类型、不同数量的数据（无数据 / 命名字段 / 元组 / 单个值）
- 不同变体同属一个枚举类型，能放进同一容器、传给同一函数。这是多个结构体做不到的
- 枚举也能在 `impl` 块里挂方法
- `Option<T>`（`Some(T)` / `None`）是标准库最重要的枚举之一，Day18 细讲

---

## 动手任务

**1.** 定义一个 `Direction` 枚举，四个变体 `Up`、`Down`、`Left`、`Right`，都不带数据。在 `main` 里各造一个值。

**2.** **直接改上面那个 `Direction`**（别新写一个，不然会报 `E0428` 名字重定义），让每个变体携带一个 `i32` 表示距离，比如 `Up(i32)`。

> 这一步我当初做错过：我以为要「新增一个枚举」，结果同一个文件里出现了两个 `Direction`，编译器报 `error[E0428]: the name 'Direction' is defined multiple times`。**任务说「改造」，那就是改原来那个。**

**3.** 给 `Message` 枚举加一个 `impl` 块，写一个方法 `print_kind(&self)`，打印 `"一条消息"` 就行。（今天还不会拆变体，Day19 学 `match` 之后再来真正区分。）

> 提示：带数据的变体这样构造 —— `Message::Write(String::from("hi"))`。

---

**配合阅读**：[官方书 · 第6章 定义枚举](https://rustwiki.org/zh-CN/book/ch06-01-defining-an-enum.html)
