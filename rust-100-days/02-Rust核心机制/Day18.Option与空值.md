# Day18 Option 与空值处理

> 目标：理解为什么 Rust 没有 `null`，并学会用 `Option<T>` 显式表达「可能没有值」，从类型层面杜绝空指针崩溃。

## 先从一个惨痛的历史说起

托尼·霍尔（Tony Hoare）1965 年发明了空引用（null reference），后来他自己称这是「价值十亿美元的错误」。为什么？因为在 Java、C++、JavaScript 这些语言里，一个引用变量**默认就可能是 null**。你调用一个看起来好好的方法，运行时却突然炸出 `NullPointerException`，而且编译器根本不提醒你——这种 bug 在生产环境里造成了无数事故。

Rust 的做法很硬核：**干脆取消 null**。那「一个函数可能查不到值」「一个数组可能索引越界」这种情况怎么表达？答案就是标准库预置的枚举 `Option<T>`。

## `Option<T>` 长什么样

它在标准库里的定义，本质就是一个枚举：

```rust
enum Option<T> {
    Some(T), // 有值：里面包着一个 T
    None,    // 没值
}
```

因为它太常用了，`Some` 和 `None` 被自动引入作用域（prelude），你不用写 `Option::Some`：

```rust
let some_number = Some(5);
let some_string = Some("一段文字");

// None 必须标注类型，编译器无法凭空推断它是哪种 Option
let absent_number: Option<i32> = None;
```

注意第三行：单独一个 `None` 时编译器不知道你想要的是 `Option<i32>` 还是 `Option<String>`，所以必须手动标注类型。

## 为什么它能消灭空指针

关键来了：`Option<i32>` 和 `i32` 是**两种不同的类型**。编译器**绝不允许**你把 `Option<i32` 当成普通 `i32` 直接用：

```rust
let x: i32 = 5;
let y: Option<i32> = Some(5);

// let sum = x + y; // ❌ 编译错误！i32 和 Option<i32> 不能直接相加
```

这行代码在别的语言里天经地义，在 Rust 里直接编译不过。为什么是好事？因为它逼你在**编译期**就把「万一 y 是 None 怎么办」想清楚。你必须先显式地把 `Option<i32>` 拆开、处理掉 `None` 的情况，才能拿到里面那个真正的 `i32`。

一句话总结：**null 的可怕之处在于「你忘了它可能是空」；Option 的厉害之处在于「你永远不可能忘记」，因为编译器盯着你。**

## 怎么取出里面的值

你可能会问：那到底怎么把 `Some(5)` 里的 `5` 拿出来？有几种方式，今天先认个脸熟：

### 1. `is_some()` / `is_none()` 先判断

```rust
let x: Option<i32> = Some(5);

if x.is_some() {
    println!("有值");
}
if x.is_none() {
    println!("没值");
}
```

### 2. `unwrap()`：有值就取出，没值就 panic

```rust
let x: Option<i32> = Some(5);
let y = x.unwrap(); // 5
println!("{}", y);

let n: Option<i32> = None;
// n.unwrap(); // ❌ 这行运行时会 panic！
```

`unwrap` 的意思是「我赌它一定有值，错了你就让程序崩」。写原型、测试时很方便，但正式代码里随便用等于把空指针异常请了回来，要谨慎。

### 3. `unwrap_or()`：没值时给个默认值

```rust
let x: Option<i32> = Some(5);
let y: Option<i32> = None;

let a = x.unwrap_or(0); // 5
let b = y.unwrap_or(0); // 0 —— None 时用默认值
```

这是处理「查不到就用默认值」场景最顺手的方法。

> 更强大的 `match` 拆法（按不同情况做不同事）我们明天 Day19 学。今天先把「Option 和 T 是不同类型、必须先拆才能用」这个观念刻进脑子。

## 标准库里到处都是 Option

一旦理解了它，你会发现 Rust 标准库到处都用 `Option` 表示「可能失败/可能查无此项」：

```rust
// Vec::get 按索引取元素，越界不会 panic，而是返回 None
let v = vec![10, 20, 30];

match v.get(10) {
    Some(val) => println!("第 10 个元素是 {}", val),
    None => println!("索引越界了"), // 走这里
}

// 字符串查找：找到返回 Some，找不到返回 None
let s = "hello";
let found = s.find('h'); // Some(0)
let not_found = s.find('z'); // None
```

对比一下：数组直接用 `v[99]` 越界会 **panic 崩溃**；用 `v.get(99)` 则安全地返回 `None`，由你决定怎么办。

## 一张表看懂

| 你想表达 | 别的语言 | Rust |
|---|---|---|
| 一定有个 `i32` | `int x` | `let x: i32` |
| 可能有、可能没有 | `Integer x`（可能是 null） | `let x: Option<i32>` |
| 判断有没有 | `if (x != null)` | `x.is_some()` / `x.is_none()` |
| 取值，没有就用默认 | 容易忘判空导致 NPE | `x.unwrap_or(默认值)` |

## 今日小结

- Rust 没有 `null`，用标准库枚举 `Option<T>` 表示「可能有值、可能没值」
- `Some(T)` 包着值，`None` 表示没值；单独写 `None` 必须标注类型
- `Option<T>` 和 `T` 是不同类型，不能直接当 `T` 用——这是编译器帮你强制处理空情况的关键
- 初步取出值：`is_some()` 判断、`unwrap()`（没值会 panic）、`unwrap_or(默认值)`
- `Vec::get`、`str::find` 等返回 `Option`，越界/找不到不再崩溃

## 动手任务

1. 写一个函数 `fn first_char(s: &str) -> Option<char>`：字符串非空时返回第一个字符，空串返回 `None`。
2. 调用它，分别传入 `"hello"` 和 `""`，用 `unwrap_or('?')` 取出值并打印（空串时应打印 `?`）。
3. 把 `let n: Option<i32> = None;` 那行的类型标注删掉，看看编译器报什么错，体会为什么 `None` 要标类型。

> 提示：取字符串第一个字符可以用 `s.chars().next()`，它本身就返回 `Option<char>`。

**配合阅读**：[官方书·第6章 Option 枚举](https://rustwiki.org/zh-CN/book/ch06-01-defining-an-enum.html)
