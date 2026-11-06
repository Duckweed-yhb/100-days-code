# Day13 字符串初探

> **前置知识**：Day12 切片（`&str` 是字符串切片）
> **难度**：入门
> **预计阅读时间**：15 分钟
> **代码说明**：每块都是独立的完整程序，单独复制进 `src/main.rs` 就能跑，换块时替换掉上一块。

---

## Rust 的字符串和别的语言不一样

很多语言里字符串就一个类型，拿来即用。Rust 里你会碰到两种：`String` 和 `&str`。刚开始觉得绕，理解了所有权就顺了。

| 类型 | 是什么 | 谁拥有它 | 类比 |
|---|---|---|---|
| `&str` | 字符串**切片**，借用一段已有的 UTF-8 文本 | 不拥有，只是借 | 借书证，能看不能改 |
| `String` | 在**堆**上分配、可增长、可修改的字符串 | 自己拥有所有权 | 你自己买的书，随便涂画 |

堆和栈 Day10 讲过：`String` 的数据放在堆上，那块内存归它管。

这阶段写程序先记住一句：**要能改、要自己管内存的用 `String`；只读借用、当参数传的用 `&str`。**

> Rust 字符串底层是 **UTF-8** 编码，存中文、英文、emoji 都没问题。关于"为什么不能用 `s[0]` 取字符"这类话题，留到 Day23 再挖，今天先用起来。

## 创建字符串

`String::new()` 造一个空字符串。这里的 `::` 读作"路径分隔符"，`String::from("...")` 的意思是"从 `String` 这个名字下面取 `from`"。模块和路径的完整规则 Day21 讲。

📦 本块能单独跑：

```rust
fn main() {
    let s1 = String::new();              // 空字符串
    let s2 = String::from("你好");        // 从字面量创建
    let s3 = "world".to_string();        // 等价写法，在字面量上加 .to_string()

    println!("s1 长度 {}，s2 = {}，s3 = {}", s1.len(), s2, s3);
}
```

常用的是 `String::from("...")`。以后要往空字符串里加东西，声明得写成 `let mut s`，看下一节。

## 修改字符串

要修改 `String`，它必须是 `mut` 的（Day03 讲的可变性）。

### 追加一段：`push_str`

📦 本块能单独跑：

```rust
fn main() {
    let mut s = String::from("foo");
    s.push_str("bar");        // 参数是一段 &str
    println!("{}", s);        // foobar
}
```

### 追加一个字符：`push`

📦 本块能单独跑：

```rust
fn main() {
    let mut s = String::from("lo");
    s.push('l');              // 参数是单个 char，注意用单引号
    println!("{}", s);        // lol
}
```

### 拼接多段：`format!`

拼好几个字符串时，`format!` 最方便。它和 `println!` 用法类似，只是它不打印，直接把拼好的结果作为一个新的 `String` 返回，并且不会拿走任何一段的所有权。名字后面带 `!` 的这类东西叫**宏**，`println!`、`format!` 都属于宏。

📦 本块能单独跑：

```rust
fn main() {
    let name = String::from("Ferris");
    let lang = String::from("Rust");
    let greeting = format!("你好，{}！欢迎学习 {}", name, lang);
    println!("{}", greeting);
    // name、lang 之后还能继续用
    println!("{} 和 {} 都还在", name, lang);
}
```

> 字符串也能用 `+` 拼接，但它有点小脾气：左边那段会被 move 走。初学阶段统一用 `format!` 最省心，`+` 等 Day23 再细说。

## 常用查询方法

| 方法 | 作用 |
|---|---|
| `s.len()` | 长度（**字节数**，不是字符数） |
| `s.is_empty()` | 是否为空，返回 `bool` |
| `s.contains("xx")` | 是否包含某段子串，返回 `bool` |
| `s.to_uppercase()` / `s.to_lowercase()` | 转大写 / 小写 |
| `s.trim()` | 去掉首尾空格、换行 |
| `s.chars()` | 得到字符迭代器，用来逐个字符遍历 |
| `s.lines()` | 按行拆成迭代器 |
| `s.replace("old", "new")` | 替换子串，返回一个新字符串 |

几个例子：

📦 本块能单独跑：

```rust
fn main() {
    let s = String::from("  Hello Rust  ");

    println!("长度：{}", s.len());                      // 字节数
    println!("包含 Rust 吗？{}", s.contains("Rust"));     // true
    println!("大写：{}", s.to_uppercase());              // "  HELLO RUST  "
    println!("去空格：{}", s.trim());                    // "Hello Rust"

    // 逐个字符遍历
    for c in "你好".chars() {                           // 每轮拿到一个 char（Day04 讲过）
        println!("字符：{}", c);
    }
}
```

顺手把 `cargo run` 跑一遍，看看输出对不对：

```powershell
cargo run
```

## 函数参数里该用 `&str` 还是 `String`

写函数接收一个字符串时，**优先用 `&str`**。它更通用：`String` 的引用能传进去，字符串字面量也能直接传进去。

📦 本块能单独跑：

```rust
fn greet(name: &str) {                 // 参数写 &str 最灵活
    println!("你好，{}！", name);
}

fn main() {
    let s = String::from("小明");
    greet(&s);        // &String 可以当成 &str 用
    greet("小红");     // 字面量本身就是 &str
}
```

> 经验：**入参用 `&str`，自己拥有、需要返回或修改时才用 `String`。** 这条原则能让你的代码兼容性最好。

## 今日小结

- `String` 是堆上、可增长、有所有权的字符串；`&str` 是借用的字符串切片。
- 创建：`String::new()`、`String::from("...")`、`"...".to_string()`。
- 修改：`push_str(...)` 追加一段、`push('x')` 追加一个字符；拼接多段用 `format!`。
- 常用：`len()`、`contains()`、`trim()`、`to_uppercase()`、`chars()`、`lines()`。
- 函数接收字符串优先写 `&str`，兼容性最好。

## 动手任务

1. 用 `format!` 拼出一句话 `"我叫XX，今年XX岁"`，其中名字和年龄用变量填进去，然后打印。
2. 给定字符串 `"  rust is fun  "`，去掉首尾空格、转成大写后打印。
3. 写一个函数 `fn shout(s: &str)`，接收一个 `&str`，打印加上感叹号的版本。分别用 `String` 的引用和字面量各调用一次。

**配合阅读**：[官方书·第8章 字符串（String 与 &str）](https://rustwiki.org/zh-CN/book/ch08-02-strings.html)
