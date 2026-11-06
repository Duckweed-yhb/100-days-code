# Day10 所有权入门

> **前置知识**：Day04 基本数据类型、Day06 函数
> **难度**：进阶
> **预计阅读时间**：25 分钟
> **目标**：理解所有权（ownership），搞清楚栈与堆、移动（move）、克隆（clone）和 `Copy`。
> **代码说明**：本章代码都放在**同一个** `main.rs` 里，按顺序往下加就能跑。有几块是**故意写错**的，前面标了 `⚠️`，照抄会编不过；标 `📦` 的都能单独跑。

---

## 为什么要有所有权

写 C/C++ 时内存得自己 `malloc` / `free`：忘了释放是内存泄漏，提前释放是悬垂指针，释放两次程序直接崩。靠垃圾回收（GC，garbage collector）的语言省心，但运行时得一直有人在旁边盯着，有额外开销，也不适合操作系统这种底层场景。

Rust 走的是第三条路：**所有权（ownership）**。它在**编译期**就把内存管理检查完，运行时不需要 GC。这也是 Rust 和别的语言最不一样的地方，新手基本都要在这里卡一阵。

## 先认识栈和堆

程序运行时的内存大致分两块：

- **栈（stack）**：像一摞餐盘，放上去、拿下来都很快。栈上的数据大小**固定**，活得也短。整数、浮点这些基本类型都在栈上。
- **堆（heap）**：像一个大仓库。要存一块大小不固定、或者可能很大的数据时，得先申请一块地方，拿到它的地址，用完再归还。`String`、`Vec` 这类能长大的数据在堆上。

所有权要管的，主要是**堆上的数据什么时候归还**。

## 三条规则

1. **每个值都有一个所有者（owner）**，也就是持有它的那个变量。
2. **同一时刻有且只有一个所有者。**
3. **当所有者离开作用域**（它所在的那对 `{}` 结束）时，这个值被自动释放。

> 作用域（scope）就是一对 `{}` 圈出来的范围。变量出了 `}` 就失效。

## 移动（move）：堆上的数据赋值后，原变量就不能用了

先看一段会报错的代码：

⚠️ 本块故意编不过（`s1` 被移走后又拿来用），别照抄：

```rust
fn main() {
    let s1 = String::from("hello");   // s1 是这个堆字符串的所有者
    let s2 = s1;                      // “把 s1 给 s2”

    println!("{}", s1);               // ❌ borrow of moved value: `s1`
}
```

为什么不让编？因为 Rust 认为：如果 `s1` 和 `s2` 都指向同一块堆内存，那 `main` 结束时两个变量都会去释放这块内存，就成了“双重释放”，C 里最危险的 bug 之一。

为了杜绝它，Rust 在 `let s2 = s1;` 这一步做了个干脆的决定：**把所有权从 `s1` 移动（move）给 `s2`，同时让 `s1` 失效**。之后只有 `s2` 是所有者，出作用域只释放一次。这就是“移动语义”。

> 比喻：像房子过户。你把房子过户给朋友，这房子法律上就是他的了，你（`s1`）不能再拿它做任何事；要处理这房子（内存），也是他（`s2`）去。

### ⚠️ 踩坑：move 之后还想用原来的变量

上面那段是这一章最常撞的一个错。编译器的原话：

```
error[E0382]: borrow of moved value: `s1`
 --> src\main.rs:5:20
  |
2 |     let s1 = String::from("hello");
  |         -- move occurs because `s1` has type `String`, which does not implement the `Copy` trait
3 |     let s2 = s1;
  |              -- value moved here
4 |
5 |     println!("{}", s1);
  |                    ^^ value borrowed here after move
  |
help: consider cloning the value if the performance cost is acceptable
  |
3 |     let s2 = s1.clone();
  |                ++++++++
```

读这条报错有个窍门：它用 `--` 标出**移走发生在哪一行**（这里第 3 行 `let s2 = s1;`），再用 `^^` 标出**你在哪一行又想用它**（第 5 行的 `s1`）。两处一对，就知道是哪个变量在半路被移走了。

教训：非 `Copy` 类型的值一旦赋给别人、或者传进函数，原变量当场作废，再读它就是这个 `E0382`。想继续用，要么改成借用（Day11 讲），要么一开始就 `.clone()`。

## 栈上的数据：赋值是复制，不会失效

那为什么 `let x = 5; let y = x;` 之后 `x` 还能用？因为整数这类存在**栈**上、大小固定、复制一份很便宜的数据，Rust 默认直接复制，不存在双重释放的问题：

📦 本块能单独跑：

```rust
fn main() {
    let x = 5;
    let y = x;          // x 在栈上，复制一份给 y，x 还能用
    println!("{} {}", x, y);   // 正常，x 和 y 都是 5
}
```

这种“赋值时自动复制、原变量不失效”的类型，实现了 **`Copy` trait**。`trait` 现在可以先理解成“一组成员能力的名字”，Day27 专门讲。Rust 里常见的 `Copy` 类型有：所有整数（`i32`、`u64` 等）、浮点（`f64`）、布尔 `bool`、字符 `char`，以及由它们组成的元组。

> 判断小窍门：一个值的全部数据都能放在栈上（不碰堆），它通常就是 `Copy` 的；`String`、`Vec` 这种在堆上分配的就不是，赋值时会 move。

## 真想要一份独立副本：clone()

堆上的数据确实想深拷贝一份（堆上新开一块内存，两边互不影响），显式调用 `.clone()`：

📦 本块能单独跑：

```rust
fn main() {
    let s1 = String::from("hello");
    let s2 = s1.clone();        // 真的复制了一份堆上的数据

    println!("s1 = {}, s2 = {}", s1, s2);   // 两个都能用，互不影响
}
```

> `clone()` 有性能代价，它真的去复制整块堆内存。所以 Rust 不会偷偷替你 clone，要你自己写出来。能用引用（明天学）就别动不动 clone。

## 函数调用也会转移所有权

把一个非 `Copy` 类型的值传给函数，同样发生 move，所有权从调用者手里交到函数的参数上：

📦 本块能单独跑：

```rust
fn main() {
    let s = String::from("hello");
    takes_ownership(s);            // s 的所有权移进函数

    // println!("{}", s);          // ❌ 到这里 s 已经失效

    let x = 5;
    makes_copy(x);                 // x 是 i32，Copy，传的是副本
    println!("{}", x);             // ✅ x 还能用
}

fn takes_ownership(some_string: String) {
    println!("{}", some_string);
}                                  // some_string 出作用域，自动释放

fn makes_copy(some_int: i32) {
    println!("{}", some_int);
}
```

函数想把所有权还回去，就把值作为返回值交出来：

📦 本块能单独跑：

```rust
fn main() {
    let s1 = gives_ownership();          // 函数造一个 String 交给 s1
    let s2 = String::from("hello");
    let s3 = takes_and_gives_back(s2);   // s2 被移走，函数又返回一个给 s3
    println!("{} {}", s1, s3);           // s2 已失效，s1、s3 有效
}

fn gives_ownership() -> String {
    String::from("我造的")
}

fn takes_and_gives_back(a: String) -> String {
    a                                    // 把 a 原样返回，所有权交回
}
```

每次都靠传参、返回值倒腾所有权太麻烦。Rust 的解法是**引用与借用**，Day11 讲。

## 今日小结

- 所有权三条规则：每个值有且仅有一个所有者；所有者离开作用域时值自动释放。
- 栈上固定大小的数据（整数、浮点、`bool`、`char`）实现了 `Copy`，赋值是复制，原变量不失效。
- 堆上数据（`String`、`Vec`）赋值或传参会发生 **move**，原变量立刻失效，报 `E0382`。这是为了让双重释放不可能发生。
- 想要独立的堆副本，显式调用 `.clone()`，它有性能开销。
- 函数传值会转移所有权；想把所有权还回去，可以把它作为返回值。

## 动手任务

1. 写一段代码：`let s1 = String::from("hi"); let s2 = s1;` 然后尝试打印 `s1`，把编译器的报错完整读一遍，对照上面的 `E0382` 看看 `--` 和 `^^` 分别指在哪儿。
2. 用 `.clone()` 改写上面的代码，让 `s1` 和 `s2` 都能正常打印。
3. 写一个函数 `fn take_len(s: String) -> usize`，返回字符串长度；观察把 `String` 传进去之后，调用处还能不能再用那个变量。

**配合阅读**：[官方书 · 第4章 什么是所有权](https://rustwiki.org/zh-CN/book/ch04-01-what-is-ownership.html)
