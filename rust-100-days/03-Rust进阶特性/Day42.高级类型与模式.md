# Day42 高级类型与模式

> 目标：认识三个平时不常写、遇到却必须认得的类型概念——类型别名、从不类型 `!`、动态大小类型 `?Sized`，并把散落各处的模式语法做一次速查汇总。

## 类型别名：type

`type` 给现有类型起个短名字，**不创建新类型**，纯粹为了少写长串、消重：

```rust
// 一个很长的闭包类型，起个短名
type Thunk = Box<dyn Fn() + Send + 'static>;

fn takes(f: Thunk) {} // 不用每次写那一大串
```

标准库自己就这么干：`std::io::Result<T>` 其实就是 `Result<T, std::io::Error>` 的别名，让你不用每次都写错误类型。

```rust
type Kilometers = i32; // 不是新类型，就是 i32 的别名

fn main() {
    let x: i32 = 5;
    let y: Kilometers = 5;
    println!("x + y = {}", x + y); // 可以直接相加，因为它俩就是同一个类型
}
```

> 想真的得到一个「不能和 i32 混用」的新类型？用 newtype 模式 `struct Kilometers(i32)`（元组结构体包一层），那才是类型安全的包装。

## 从不类型 !

`!` 叫**从不类型（never type）**，表示「这个表达式永远不会产生值」。它能被强制转换成任意其他类型。

哪些东西的类型是 `!`？`panic!`、`continue`、`break`、永不结束的 `loop`：

```rust
fn main() {
    let guess: u32 = match "3".parse() {
        Ok(n) => n,        // 这个分支产出 u32
        Err(_) => continue, // 这个分支是 !，它「永不返回」
    }; // 因为 ! 能适配任何类型，整个 match 的类型就是 u32
    println!("{}", guess);
}
```

你平时几乎不会手写 `!`，但要知道为什么 `match` 一个分支 `continue` 也能和 `u32` 分支放在一起而不报类型错误。

## 动态大小类型与 ?Sized

Rust 编译时必须知道每个类型占多少字节。但有些类型**运行时才知道大小**，叫动态大小类型（Dynamically Sized Type, DST）：

- `str`（不是 `&str`）：字符串内容多长编译时不知道
- `dyn Trait`：具体是哪个类型编译时不知道

这些类型**不能直接当变量、当参数**，必须放在指针后面：`&str`、`Box<str>`、`Box<dyn Trait>`。你天天用的 `&str` 其实是「指针 + 长度」两个值。

和 DST 配套的是 `Sized` trait：编译期已知大小的类型自动实现它。编译器**隐式**给每个泛型函数加上 `Sized` 约束：

```rust
// 你写的：
fn generic<T>(t: &T) {}
// 编译器默认理解为：fn generic<T: Sized>(t: &T) {}
```

如果你的泛型参数**可能**是 DST，要用 `?Sized` 显式放宽：

```rust
fn generic<T: ?Sized>(t: &T) {} // T 可能不定长，但我们只通过引用传它
```

> `?Sized` 是泛型里少有的「默认是 Sized，我主动放宽」场景。现在认得即可，写库时才会常用。

## 模式语法速查

我们在 Day19、Day20 已经会用 `match`、`if let`。这里把所有模式语法一网打尽，当字典查：

```rust
fn main() {
    // 1. 匹配字面量
    let x = 2;
    match x {
        1 => println!("一"),
        2 | 3 => println!("二或三"), // | 或
        4..=10 => println!("4到10"), // 闭区间
        _ => println!("其他"),
    }
}
```

更全的速查表：

| 语法 | 例子 | 作用 |
|---|---|---|
| 字面量 | `1`、`"hello"` | 精确匹配值 |
| `\|` | `1 \| 2` | 多模式或 |
| 区间 | `1..=5`、`'a'..='z'` | 闭区间（数字/字符） |
| 解构结构体 | `Point { x, y }` | 拆出字段 |
| 解构枚举 | `Message::Write(s)` | 拆出变体里的数据 |
| `_` | `(_, y)` | 忽略单个值，不绑定 |
| `_x` | `(_x, y)` | 忽略但仍绑定（消警告） |
| `..` | `Point { x, .. }` | 忽略剩余所有字段 |
| 匹配守卫 | `Some(x) if x > 5` | 模式后加额外条件 |
| `@` 绑定 | `id: n @ 3..=7` | 测试范围同时绑定变量 |

几个有意思的例子：

```rust
// 解构结构体
struct Point { x: i32, y: i32 }
let p = Point { x: 0, y: 7 };
let Point { x, y } = p; // x=0, y=7

// 匹配守卫：模式写不出的额外条件
let num = Some(4);
match num {
    Some(x) if x < 5 => println!("小于5: {}", x),
    Some(x) => println!("{}", x),
    None => (),
}

// @ 绑定：一边测范围一边存变量
enum Message { Hello { id: i32 } }
let msg = Message::Hello { id: 5 };
match msg {
    Message::Hello { id: n @ 3..=7 } => println!("id={} 在3~7", n),
    Message::Hello { id } => println!("其他 id={}", id),
}
```

## 今日小结

- `type` 给类型起别名，不产生新类型；要新类型用 newtype 元组结构体
- 从不类型 `!`：永不返回值的表达式（`panic!`/`continue`/`break`/死循环），可适配任意类型
- DST（`str`、`dyn Trait`）编译期不知大小，必须放指针后；泛型默认 `Sized`，放宽用 `?Sized`
- 模式语法速查：字面量、`|`、`..=`、解构、`_`/`..`、匹配守卫 `if`、`@` 绑定

## 动手任务

1. 用 `type` 给 `HashMap<String, Vec<i32>>` 起个短别名，写个函数用它。
2. 写一个函数参数是 `&str`，验证它和 `&String` 都能传（体会 DST 引用）。
3. 用 `@` 绑定写一段：匹配一个 `Message::Hello { id }`，要求 id 在 10~20 时打印出这个 id 值。

> `..` 必须无歧义，`(.., second, ..)` 会编译报错——它不知道忽略几个。

**配合阅读**：[官方书·第19章 高级类型](https://rustwiki.org/zh-CN/book/ch19-04-advanced-types.html)
