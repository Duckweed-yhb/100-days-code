# Day23 字符串 String 深入

> **前置知识**：Day10 所有权（`+` 会移动左边的 String）、Day12 切片（`[a..b]` 范围切片）、Day13 字符串初探
> **难度**：进阶
> **预计阅读时间**：25 分钟
> **代码说明**：本章代码都放在**同一个** `main.rs` 里。每块都带完整的 `fn main()`，单独复制就能跑。
> 每块前面标了 `📦`（能单独跑）或 `⚠️`（给你看的，抄了会出问题）。两块的 `main` 不能同时塞进一个文件。

---

## 先分清两种字符串

Rust 里的「字符串」是两个类型，这是最容易混的地方：

| 类型 | 是什么 | 所有权 |
|---|---|---|
| `&str` | 字符串切片，借用一段已经存在的 UTF-8 数据 | 没有，是借来的 |
| `String` | 堆上分配、能增长、能改的 UTF-8 字符串 | 有 |

字符串字面量 `"hello"` 的类型是 `&str`，内容硬编码在编译出来的程序里，改不了。`String` 是标准库给的类型，运行时在堆上拼出来，可以加长、可以改。Day13 初见过，今天细看。

`&str` 前面那个 `&` 就是 Day11 的借用符号，意思是「我只看着这段数据，不拥有它」。

## 创建 String

📦 本块能单独跑：

```rust
fn main() {
    // 空字符串
    let mut s = String::new();
    s.push_str("你好");
    println!("{}", s); // 你好

    // 从字面量造，两种写法等价
    let s1 = String::from("你好");
    let s2 = "你好".to_string();
    println!("{} {}", s1, s2);
}
```

内部存的是 UTF-8 字节，所以中文、日文、西里尔字母都放得下：`"你好"`、`"こんにちは"` 都合法。

`String::new()` 造出来是空的，要往里加东西就得加 `mut`，这是 Day03 的可变性规则。

## push_str 和 push

📦 本块能单独跑：

```rust
fn main() {
    let mut s = String::from("foo");
    s.push_str("bar"); // 追加一段字符串，s 变成 "foobar"

    // push_str 的参数是 &str：不拿走 s2 的所有权，s2 后面还能用
    let s2 = "baz";
    s.push_str(s2);
    println!("s2 还能用: {}", s2); // baz

    // push 只追加一个字符，参数是 char，用单引号写
    s.push('!');
    println!("{}", s); // foobarbaz!
}
```

`push_str` 和 `push` 的区别就在参数上：前者收 `&str`（一段字符串），后者收 `char`（单个字符，用单引号）。

## 用 + 和 format! 拼接

`+` 用起来有个容易踩的地方，先看代码：

📦 本块能单独跑：

```rust
fn main() {
    let s1 = String::from("Hello, ");
    let s2 = String::from("world!");
    let s3 = s1 + &s2; // s1 被移动走了，之后不能再用 s1；s2 只是借用，还在

    println!("{}", s3);           // Hello, world!
    println!("s2 还能用: {}", s2); // world!
}
```

`+` 的签名是 `fn add(self, s: &str) -> String`。第一个参数是 `self`，也就是说左边的 `String` 会被**移动**进函数，用完就没了；第二个参数是 `&str`，右边传引用就行，所以得写 `&s2`。要拼三段以上，`+` 会越写越乱。

多段拼接用 `format!` 最舒服，它**不移动任何变量**：

📦 本块能单独跑：

```rust
fn main() {
    let s1 = String::from("tic");
    let s2 = String::from("tac");
    let s3 = String::from("toe");

    let s = format!("{}-{}-{}", s1, s2, s3); // "tic-tac-toe"
    println!("{}", s);
    println!("{} {} {}", s1, s2, s3); // 三个原变量都还能用
}
```

`format!` 和 `println!` 是同一套占位符写法，区别只是它把结果返回成 `String`，不打印。

## 为什么不能写 s[0]

这是 Rust 字符串最别扭的一条规则：别的语言里 `s[0]` 随手就写，Rust 直接不给编译。

⚠️ 本块编译不过，贴出来给你看错误长什么样：

```rust
fn main() {
    let s = String::from("hello");
    let h = s[0];
    println!("{}", h);
}
```

编译器原话（只留了前几行）：

```
error[E0277]: the type `str` cannot be indexed by `{integer}`
 --> main.rs:3:15
  |
3 |     let h = s[0];
  |               ^ string indices are ranges of `usize`
  |
  = help: the trait `SliceIndex<str>` is not implemented for `{integer}`
  = note: you can use `.chars().nth()` or `.bytes().nth()`
```

原因在 UTF-8 这个编码上：

- `String` 底层是一个 `Vec<u8>`（Day22 的 Vec）。英文一个字符占 1 字节，中文、西里尔字母一个字符要占 2 到 4 字节。
- 如果允许 `s[0]`，对 `"你好"` 来说返回的就是某个汉字的半个字节，不是有效字符。
- 下标取值本应该是 O(1) 常数时间，但 UTF-8 下想定位第 n 个字符得从头数，做不到常数时间。

所以 Rust 干脆禁掉了这种会误导人的操作。

## 切片按字节来，边界要踩对

不能取单个字符，但可以取一段**字节切片** `[起始..结束]`，就是 Day12 学的范围切片写法。

📦 本块能单独跑：

```rust
fn main() {
    let hello = "Здравствуйте"; // 西里尔字母，每个字符占 2 字节
    let s = &hello[0..4]; // 取第 0 到第 3 个字节，正好是两个完整字符
    println!("{}", s); // Зд
}
```

英文 ASCII 一个字符 1 字节，随便切都不会错。多字节文字就不一样了，切点要是没落在字符边界上，程序直接 panic：

⚠️ 本块能编译，但一运行就 panic（想看效果就抄去跑一遍）：

```rust
fn main() {
    let hello = "Здравствуйте";
    let bad = &hello[0..1];
    println!("{}", bad);
}
```

运行结果（括号里的线程号每次都不一样，这里省了）：

```
thread 'main' panicked at main.rs:3:21:
byte index 1 is not a char boundary; it is inside 'З' (bytes 0..2) of `Здравствуйте`
note: run with `RUST_BACKTRACE=1` environment variable to display a backtrace
```

`0..1` 只取了 `З` 的第一个字节，可 `З` 占 2 字节，正好切在中间。切多字节字符串之前先确认切点落在字符边界上：`"你好"` 每个汉字占 3 字节，切 `0..3` 得到完整的 `你`，切 `0..1` 就崩。

## 要字符还是要字节

想逐个字符处理，用 `.chars()`，拿到的是 `char`（Rust 的字符类型，一个 Unicode 标量值，占 4 字节）：

📦 本块能单独跑：

```rust
fn main() {
    for c in "你好".chars() {
        println!("{}", c); // 先 你，再 好
    }
}
```

想逐个字节处理，用 `.bytes()`，拿到的是 u8（0 到 255 的整数）：

📦 本块能单独跑：

```rust
fn main() {
    for b in "你好".bytes() {
        println!("{}", b); // 6 个 u8，每个汉字占 3 字节
    }
}
```

还有个概念要记住：人眼里的「一个字」在 Unicode 里不一定是一个 `char`。比如带音调的字母可能是「字母 + 音调符号」两个码位拼出来的，emoji 更是常由好几个码位组合。这种「字形簇」标准库不直接提供，需要去 crates.io 找专门的 crate。

## 常用方法速查

| 操作 | 写法 | 说明 |
|---|---|---|
| 空串 | `String::new()` | 要改得加 `mut` |
| 从字面量 | `String::from("...")` / `"...".to_string()` | 等价 |
| 追加串 | `s.push_str("...")` | 不拿走参数所有权 |
| 追加字符 | `s.push('a')` | 单字符用单引号 |
| 拼接 | `s1 + &s2` | s1 被移动 |
| 多段拼 | `format!("{}-{}", s1, s2)` | 不移动任何变量 |
| 长度 | `s.len()` | 字节数，不是字符数 |
| 是否为空 | `s.is_empty()` | |
| 去首尾空白 | `s.trim()` | |
| 按空白分词 | `s.split_whitespace()` | 返回迭代器 |
| 按字符遍历 | `s.chars()` | |
| 按字节遍历 | `s.bytes()` | |

> `s.len()` 返回的是**字节数**。`"你好".len()` 是 6，不是 2，这是 UTF-8 带来的认知差。

## 今日小结

- `&str` 是借来的字符串切片，`String` 是堆上有所有权、能增长的 UTF-8 串
- 追加用 `push_str` / `push`；两段拼用 `+`（左边那个会被移动），多段拼用 `format!`
- Rust 禁止 `s[0]` 索引，因为 UTF-8 下一个字符可能占多个字节，按字节下标会切出半个字符
- 字节切片 `&s[0..4]` 必须落在字符边界上，否则运行时 panic
- 遍历用 `.chars()`（字符）或 `.bytes()`（字节）；`len()` 给的是字节数

## 动手任务

1. 用 `format!` 把 `"My"`、`"name"`、`"is"` 和你的名字拼成一句完整问候，确认三个原变量之后还能用。
2. 拿 `"你好世界"` 用 `.chars()` 遍历打印每个字符，再打印 `.len()`，看看为什么 len 是 12 而不是 4。
3. 把 `"你好世界"` 存进一个变量，对它取 `&s[0..1]`，故意触发一次 char boundary panic（加上 `RUST_BACKTRACE=1` 观察），加深印象。

> 第 1 题提示：`format!` 的结果要赋给一个变量才能再打印，它本身不打印任何东西。
> 第 3 题提示：PowerShell 下加环境变量的写法是 `$env:RUST_BACKTRACE=1; cargo run`。
> `split_whitespace()` 可以把 `"hello world"` 切成 `"hello"` 和 `"world"` 两段，Day24 的词频统计会用到。

**配合阅读**：[官方书·第8章 使用字符串](https://rustwiki.org/zh-CN/book/ch08-02-strings.html)
