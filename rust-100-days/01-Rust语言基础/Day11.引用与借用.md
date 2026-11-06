# Day11 引用与借用

> **前置知识**：Day10 所有权（移动 move、函数调用转移所有权）
> **难度**：入门
> **预计阅读时间**：15 分钟
> **代码说明**：每块都是**独立**的完整程序，单独复制进 `src/main.rs` 就能跑。
> `📦` 的块能跑通；`⚠️` 的块是我故意写错的，编译会报错，只给你看编译器怎么拦，**别抄**。
> 因为每块自己都带了 `main`，所以别把两块直接摞进同一个文件（那样会报 `E0428` 名字重复）。换块的时候，把上一块替换掉。

---

## 昨天的麻烦

Day10 里我们发现：把 `String` 交给函数，所有权就被 move 走了，调用处不能再用。想继续用它，只能把它当返回值再传回来，写起来很啰嗦。

Rust 的解法是**引用（reference）**。所有权不交出去，只是把数据借给函数看一眼。

## 不可变引用 `&`

在变量前加一个 `&`，拿到的是它的**引用**：数据所在的地址。它像一张能看不能改的借书证。

📦 本块能单独跑：

```rust
fn main() {
    let s = String::from("hello");

    let len = calculate_length(&s);   // 传的是 s 的引用，不是 s 本身

    println!("'{}' 的长度是 {}", s, len);   // s 还能用，所有权没动
}

fn calculate_length(s: &String) -> usize {   // 参数写 &String：借一个 String 来看
    s.len()
}
```

- `&s`：指向 `s` 的引用。数据本身还是 `s` 的，引用只是借来看。
- 参数写成 `&String`，意思是"我借一个 `String` 来看"。
- 因为它不拥有数据，函数结束时不会释放 `s` 的数据。
- `usize` 是无符号整数，专门用来表示长度和下标（Day04 讲过）。

这个动作叫**借用（borrowing）**：借出去看一眼，用完还回来，所有权一直在原主人手里。

### 借来的东西默认不能改

不可变引用只能看。下面这块我故意让它去改数据，编译就过不去了。

⚠️ 本块编译不过，贴出来是给你看编译器怎么拦：

```rust
fn main() {
    let s = String::from("hello");
    change(&s);
}

fn change(some_string: &String) {
    some_string.push_str(", world");   // ❌ 不能修改不可变引用
}
```

编译器原话：

```
error[E0596]: cannot borrow `*some_string` as mutable, as it is behind a `&` reference
 --> src\main.rs:7:5
  |
7 |     some_string.push_str(", world");
  |     ^^^^^^^^^^^ `some_string` is a `&` reference, so the data it refers to cannot be borrowed as mutable
  |
help: consider changing this to be a mutable reference
  |
6 | fn change(some_string: &mut String) {
  |                         +++
```

`push_str` 是往字符串末尾追加一段内容，它需要改动数据，而 `&String` 没给这个权限。编译器的 help 已经把答案递到手上：参数改成 `&mut String`。

## 可变引用 `&mut`

想通过引用改数据，就要借"可变引用"，写法是 `&mut`。需要两步：变量本身声明为 `mut`，再传 `&mut`。

📦 本块能单独跑：

```rust
fn main() {
    let mut s = String::from("hello");   // 1. 变量要 mut
    change(&mut s);                      // 2. 传可变引用 &mut s
    println!("{}", s);                   // hello, world
}

fn change(some_string: &mut String) {   // 参数也写 &mut String
    some_string.push_str(", world");
}
```

> 三件套缺一不可：`let mut s`、调用处 `&mut s`、函数签名 `&mut String`。少一个都会报错。

## 借用规则

Rust 要在编译期就杜绝数据竞争（两处同时读写同一块内存，结果谁也说不准），所以对借用定了两条铁律：

1. 在**同一时刻**，要么只能有**多个不可变引用**（大家一起看），要么只能有**一个可变引用**（独占着改）。两者不能同时存在。
2. 引用必须始终有效，不能出现"悬垂引用"。

打个比方：

- 不可变引用 = 一屋子人同看一份报纸，没问题。
- 可变引用 = 一个人拿笔在报纸上改稿，这时候别人不能看，更不能同时改。

先看合法的顺序使用：

📦 本块能单独跑：

```rust
fn main() {
    let mut s = String::from("hello");

    let r1 = &s;
    let r2 = &s;              // ✅ 多个不可变引用同时存在，OK
    println!("{} {}", r1, r2);

    let r3 = &mut s;          // ✅ r1、r2 最后一次使用已经过去了，现在可以独占
    r3.push_str(" world");
    println!("{}", r3);
}
```

再看会报错的"同时并存"：

⚠️ 本块编译不过：

```rust
fn main() {
    let mut s = String::from("hello");

    let r1 = &s;          // 不可变引用
    let r2 = &mut s;      // ❌ 报错：cannot borrow `s` as mutable
    println!("{} {}", r1, r2);
}
```

编译器原话：

```
error[E0502]: cannot borrow `s` as mutable because it is also borrowed as immutable
 --> src\main.rs:5:14
  |
4 |     let r1 = &s;
  |              -- immutable borrow occurs here
5 |     let r2 = &mut s;
  |              ^^^^^^ mutable borrow occurs here
6 |     println!("{} {}", r1, r2);
  |                       -- immutable borrow later used here
```

注意最后一行 `immutable borrow later used here`：它指的不是"你声明了 `r2`"，而是"你还在用 `r1`"。

这引出一条好消息：引用的作用域到**最后一次使用**为止，不是到 `}` 为止。上面那个合法例子里，`r1`、`r2` 用完就能开 `&mut s`。编译器按"实际用到哪里"判断，不会乱拦你。

## 解引用 `*`

拿到引用后，如果要拿到它背后的**值本身**，在引用前面加 `*`。这个操作叫**解引用（dereference）**。

📦 本块能单独跑：

```rust
fn main() {
    let x = 10;
    let r = &x;            // r 的类型是 &i32
    println!("{}", r);     // 直接打印也行，println! 会自动解引用
    println!("{}", *r);    // 手动解引用，得到 10

    let mut y = 5;
    let rr = &mut y;
    *rr += 1;              // 通过可变引用修改 y
    println!("{}", y);     // 6
}
```

> `s.len()` 这种方法调用，Rust 会自动帮你解引用，所以不用到处写 `*`。知道 `*` 是"取引用背后的值"就够了。

## 悬垂引用（dangling reference）

一个值已经被释放，却还留着引用指向它，这就是**悬垂引用**。C 里这类指针是经典崩溃来源，Rust 直接在编译期禁止：

⚠️ 本块编译不过：

```rust
fn main() {
    let reference_to_nothing = dangle();
}

fn dangle() -> &String {        // ❌ 返回一个 String 的引用
    let s = String::from("hello");
    s                           // 函数结束，s 被释放；
}                               // 返回它的引用，等于交回一个指向空处的地址
```

编译器原话（最后一条 help 直接把正确写法给了你）：

```
error[E0106]: missing lifetime specifier
 --> src\main.rs:5:16
  |
5 | fn dangle() -> &String {
  |                ^ expected named lifetime parameter
  |
  = help: this function's return type contains a borrowed value, but there is no value for it to be borrowed from
help: instead, you are more likely to want to return an owned value
  |
5 - fn dangle() -> &String {
5 + fn dangle() -> String {
```

正确做法是把 `String` 本身返回出去，让所有权随返回值一起交给调用者：

📦 本块能单独跑：

```rust
fn main() {
    let s = no_dangle();
    println!("{}", s);
}

fn no_dangle() -> String {      // 返回 String，不是 &String
    let s = String::from("hello");
    s                            // 所有权移交给调用者，不会被提前释放
}
```

### ⚠️ 踩坑：忘了写 `&`，参数把所有权吃掉了

这个坑我踩过。写 `calculate_length` 的时候顺手把参数写成了 `s: String`，`&` 忘了加。第一行看着毫无问题：

⚠️ 本块编译不过：

```rust
fn calculate_length(s: String) -> usize {
    s.len()
}

fn main() {
    let s = String::from("hello");
    let len = calculate_length(s);
    println!("{} 的长度是 {}", s, len);   // ❌ 炸在这里
}
```

编译器原话：

```
error[E0382]: borrow of moved value: `s`
 --> src\main.rs:8:28
  |
6 |     let s = String::from("hello");
  |         - move occurs because `s` has type `String`, which does not implement the `Copy` trait
7 |     let len = calculate_length(s);
  |                                - value moved here
8 |     println!("{} 的长度是 {}", s, len);
  |                                ^ value borrowed here after move
  |
note: consider changing this parameter type in function `calculate_length` to borrow instead if owning the value isn't necessary
 --> src\main.rs:1:24
  |
1 | fn calculate_length(s: String) -> usize {
  |    ----------------    ^^^^^^ this parameter takes ownership of the value
```

教训：`String` 这种堆上数据没有实现 `Copy`，传参就是 move。想借，参数上必须写 `&` 或 `&mut`。报错还会出现在**移动之后的某一行**，因为移动发生的那一刻编译器不说话，等你再用那个变量才报出来。看到 `E0382` 和 "moved value"，先回去看参数类型是不是漏了 `&`。

## 今日小结

- 引用不拿走所有权：`&s` 是不可变引用（能看不能改），`&mut s` 是可变引用（能改）。
- 函数参数写成 `&String` / `&mut String` 表示"借用"，函数结束不会释放数据。
- 借用规则：同一时刻，要么多个不可变引用，要么一个可变引用，**不能混着来**。
- 引用的作用域到最后一次使用为止，所以"先用不可变、后用可变"是合法的。
- 用 `*` 解引用拿到背后的值；方法调用会自动解引用。
- 不能返回已经释放变量的引用（悬垂引用）；要返回就直接返回值本身。

## 动手任务

1. 写一个函数 `fn calculate_length(s: &String) -> usize`，用不可变引用求字符串长度。在 `main` 里传完引用之后，再 `println!` 一次原变量，确认它还能用。
2. 写一个函数 `fn append_world(s: &mut String)`，通过可变引用给字符串末尾加上 `", world"`。在 `main` 里验证原字符串被改了。
3. 故意在同一个作用域里同时写一个 `&s` 和一个 `&mut s`，并且两个都用到（都 `println!` 一遍），读一遍编译器报的 `E0502`。然后把顺序改回合法写法，再跑一次，确认能过。

**配合阅读**：[官方书·第4章 引用与借用](https://rustwiki.org/zh-CN/book/ch04-02-references-and-borrowing.html)
