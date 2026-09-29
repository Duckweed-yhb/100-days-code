# Day13 字符串初探

> 目标：分清 Rust 里最常见的两种字符串 `String` 和 `&str`，学会创建、拼接、查询字符串的常用方法。

## Rust 的字符串和别的语言不一样

很多语言里"字符串"就一个类型，拿来即用。Rust 里你迟早会碰到两种：`String` 和 `&str`。新手刚开始觉得绕，但理解了所有权就很自然。

| 类型 | 是什么 | 谁拥有它 | 类比 |
|---|---|---|---|
| `&str` | 字符串**切片**，借用一段已有的 UTF-8 文本 | 不拥有，只是借 | 借书证，能看不能改 |
| `String` | 在**堆**上分配、可增长、可修改的字符串 | 自己拥有所有权 | 你自己买的书，随便涂画 |

我们这阶段写程序，记住一句话：**要能变、要自己管内存的用 `String`；只读借用、当参数传的用 `&str`。**

> Rust 字符串底层是 **UTF-8** 编码，存中文、英文、emoji 都没问题。关于"为什么不能用 `s[0]` 取字符"这类深入话题，我们留到 Day23 再挖，今天先用起来。

## 创建字符串

### 1. 空字符串

```rust
let mut s = String::new();   // 一个空字符串，需要 mut 才能往里加东西
```

### 2. 从字面量创建（两种等价写法）

```rust
let s1 = String::from("你好");           // 写法一：from
let s2 = "world".to_string();             // 写法二：to_string
```

最常用的是 `String::from("...")`。

## 修改字符串

要修改 `String`，它必须是 `mut` 的。

### 追加一段：`push_str`

```rust
fn main() {
    let mut s = String::from("foo");
    s.push_str("bar");        // 追加一段字符串
    println!("{}", s);        // foobar
}
```

### 追加一个字符：`push`

```rust
let mut s = String::from("lo");
s.push('l');                 // 注意是单引号，一个字符
println!("{}", s);          // lol
```

### 拼接多段：`format!`

拼好几个字符串时，最方便的是 `format!` 宏——它和 `println!` 类似，但不打印，而是返回一个新的 `String`，而且不会拿走任何一段的所有权：

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

> 字符串也能用 `+` 拼接，但它有个小脾气（左边那个会被"移动"掉），初学阶段**统一用 `format!`** 最省心，等 Day23 再细说 `+`。

## 常用查询方法

| 方法 | 作用 |
|---|---|
| `s.len()` | 长度（**字节数**，不是字符数） |
| `s.is_empty()` | 是否为空 |
| `s.contains("xx")` | 是否包含某段子串，返回 `bool` |
| `s.to_uppercase()` / `s.to_lowercase()` | 转大写 / 小写 |
| `s.trim()` | 去掉首尾空格、换行 |
| `s.chars()` | 得到字符迭代器，用来逐个字符遍历 |
| `s.lines()` | 按行拆成迭代器 |
| `s.replace("old", "new")` | 替换子串 |

几个例子：

```rust
fn main() {
    let s = String::from("  Hello Rust  ");

    println!("长度：{}", s.len());              // 字节数
    println!("包含 Rust 吗？{}", s.contains("Rust"));   // true
    println!("大写：{}", s.to_uppercase());      // "  HELLO RUST  "
    println!("去空格：{}", s.trim());            // "Hello Rust"

    // 逐个字符遍历
    for c in "你好".chars() {
        println!("字符：{}", c);
    }
}
```

运行下面命令看看输出：

```powershell
cargo run
```

## 函数参数里该用 `&str` 还是 `String`

写函数接收一个字符串时，**优先用 `&str`**——因为它更通用：不管是 `String`（传 `&s`）还是字面量 `"..."` 都能传进来。

```rust
fn greet(name: &str) {                 // 参数写 &str 最灵活
    println!("你好，{}！", name);
}

fn main() {
    let s = String::from("小明");
    greet(&s);        // String 传引用
    greet("小红");     // 字面量直接就是 &str
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

1. 用 `format!` 拼出一句话：`"我叫XX，今年XX岁"`，其中名字和年龄用变量填进去。
2. 给定字符串 `"  rust is fun  "`，把它去掉首尾空格、转成大写后打印。
3. 写一个函数 `fn shout(s: &str)`，接收一个 `&str`，打印加上感叹号的版本；分别用 `String` 引用和字面量各调用一次。

**配合阅读**：[官方书·第8章 字符串（String 与 &str）](https://rustwiki.org/zh-CN/book/ch08-02-strings.html)
