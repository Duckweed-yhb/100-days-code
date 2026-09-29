# Day23 字符串 String 深入

> 目标：彻底搞清楚 `String` 和 `&str` 的区别，掌握字符串的增、拼、查、遍历，并理解为什么 Rust 不允许 `s[0]` 索引。

## 先分清两种字符串

Rust 里「字符串」其实是两个类型：

| 类型 | 是什么 | 所有权 |
|---|---|---|
| `&str` | 字符串切片，借用一段已有 UTF-8 数据 | 无所有权，是借来的 |
| `String` | 堆上分配、可增长、可变的 UTF-8 字符串 | 有所有权 |

你写的字符串字面量 `"hello"` 类型是 `&str`，它硬编码在程序二进制里；`String` 是标准库提供的、运行时才在堆上拼出来的可变字符串。Day13 我们初见过，今天深入。

## 创建 String

```rust
// 空字符串
let mut s = String::new();

// 从字面量创建（两种等价写法）
let s1 = String::from("你好");
let s2 = "你好".to_string();
```

它内部是 UTF-8 编码，所以中文、印地语、日文都能存：`"你好"`、`"こんにちは"` 都合法。

## 更新字符串

### 追加：`push_str` 和 `push`

```rust
let mut s = String::from("foo");
s.push_str("bar"); // 追加一段字符串，s 变成 "foobar"

// push_str 接收 &str，不拿走参数所有权，所以 s2 之后还能用
let s2 = "baz";
s.push_str(s2);
println!("s2 还能用: {}", s2);

// push 只追加单个字符（单引号）
s.push('!');
println!("{}", s); // foobar baz! （连起来是 foobarbaz!）
```

### 拼接：`+` 和 `format!`

```rust
let s1 = String::from("Hello, ");
let s2 = String::from("world!");
let s3 = s1 + &s2; // 注意：s1 被移动了，之后不能再用；s2 传引用还能用
```

`+` 的签名其实是 `fn add(self, s: &str) -> String`，所以左边那个 `String` 会被吃掉（移动），右边传 `&str`。这也是为什么拼两段以上时 `+` 写起来很别扭。

多段拼接用 `format!` 最舒服，它**不移动任何变量**：

```rust
let s1 = String::from("tic");
let s2 = String::from("tac");
let s3 = String::from("toe");

let s = format!("{}-{}-{}", s1, s2, s3); // "tic-tac-toe"
// s1、s2、s3 之后都还能用
```

## 为什么不能用 `s[0]` 索引

这是 Rust 字符串最反直觉的一点：

```rust
let s = String::from("hello");
// let h = s[0]; // ❌ 编译错误！
```

原因在 UTF-8 编码：

- `String` 底层是 `Vec<u8>`。英文一个字符占 1 字节，但中文、西里尔字母一个字符可能占 2~4 字节。
- 如果允许 `s[0]`，对 `"你好"` 来说返回的是半个汉字（一个无意义的字节），根本不是有效字符。
- 而且索引操作本应 O(1)，但 UTF-8 下定位第 n 个字符必须从头数，没法做到常数时间。

所以 Rust 直接禁止了这种会误导人的操作。

## 切片：按字节范围，但要小心

不能用 `[]` 取单个字符，但可以用 `[起始..结束]` 取一段**字节切片**：

```rust
let hello = "Здравствуйте"; // 西里尔语，每个字符占 2 字节
let s = &hello[0..4]; // "Зд" —— 正好落在两个完整字符边界上

// let bad = &hello[0..1]; // ❌ 运行时 panic：byte index 1 is not a char boundary
```

英文 ASCII 每个字符 1 字节，随便切都安全；但多字节文字（比如中文）切片时，边界没踩在字符上会直接 panic。**中文串切片前务必确认长度是偶数个字符对应的字节数。**

## 遍历：你想要字符还是字节

想逐字符处理，用 `.chars()`：

```rust
for c in "你好".chars() {
    println!("{}", c); // 你，然后 好
}
```

想逐字节处理，用 `.bytes()`：

```rust
for b in "你好".bytes() {
    println!("{}", b); // 输出 6 个 u8（每个汉字 3 字节）
}
```

注意：人眼里的一个字（比如带音调的组合字符）在 Unicode 里可能由多个码位组成，这种「字形簇」标准库不直接提供，需要去 crates.io 找专门的 crate。

## 常用方法速查

| 操作 | 写法 | 说明 |
|---|---|---|
| 空串 | `String::new()` | 要改得 `mut` |
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

> `s.len()` 返回的是**字节数**。`"你好".len()` 是 6，不是 2——这又是 UTF-8 带来的认知差。

## 今日小结

- `&str` 是借来的字符串切片，`String` 是堆上有所有权、可增长的 UTF-8 串
- 追加用 `push_str`/`push`；两段拼用 `+`（左边会被移动），多段拼用 `format!`
- Rust 禁止 `s[0]` 索引，因为 UTF-8 下一个字符可能多字节，索引会切出半个字符
- 字节切片 `&s[0..4]` 必须落在字符边界，否则运行时 panic
- 遍历用 `.chars()`（字符）或 `.bytes()`（字节）；`len()` 是字节数

## 动手任务

1. 用 `format!` 把 `"My"`、`"name"`、`"is"` 和你的名字拼成一句完整问候，确认三个原变量还能用。
2. 写一个字符串 `"你好世界"`，用 `.chars()` 遍历并打印每个字符，再打印 `.len()`，观察为什么 len 是 12 而不是 4。
3. 尝试对 `"你好世界"` 取 `&s[0..1]`，故意触发一次 char boundary panic（用 `RUST_BACKTRACE=1` 观察），加深印象。

> 提示：`split_whitespace()` 可以把 `"hello world".split_whitespace()` 分成 `"hello"` 和 `"world"` 两段，Day24 单词计数会用到。

**配合阅读**：[官方书·第8章 使用字符串](https://rustwiki.org/zh-CN/book/ch08-02-strings.html)
