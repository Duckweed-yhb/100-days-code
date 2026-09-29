# Day11 引用与借用

> 目标：学会用 `&` 不可变引用和 `&mut` 可变引用"借"数据，理解 Rust 著名的借用规则，以及为什么它能杜绝数据竞争。

## 昨天的麻烦

Day10 我们发现：把 `String` 直接传给函数，所有权就被 move 走了，调用处不能再用。为了继续用它，你得把它当返回值再传回来，非常啰嗦。

Rust 的解法是**引用（reference）**：你不把所有权交出去，只是"借"给函数看一眼。

## 不可变引用 `&`

在变量前面加 `&`，就拿到了它的**引用**——像一张"能看但不能改"的借书证：

```rust
fn main() {
    let s = String::from("hello");

    let len = calculate_length(&s);   // 把 s 的引用传进去，不是 move

    println!("'{}' 的长度是 {}", s, len);   // ✅ s 还能用，所有权没动
}

fn calculate_length(s: &String) -> usize {   // 参数是 &String：引用
    s.len()
}                                            // s 只是个引用，不拥有数据，
                                             // 出作用域也不会释放什么
```

- `&s`：创建一个指向 `s` 的引用，但**不拥有**它。
- 函数参数写成 `&String`，表示"我借一个 String 来看"。
- 因为不拥有，函数结束时它指向的数据**不会被释放**。

这个动作叫**借用（borrowing）**。

### 默认不能通过引用修改

不可变引用只能看，不能改。下面会报错：

```rust
fn main() {
    let s = String::from("hello");
    change(&s);
}

fn change(some_string: &String) {
    some_string.push_str(", world");   // ❌ 不能修改不可变引用
}
```

## 可变引用 `&mut`

想通过引用改数据，就要借"可变引用"。需要两步：变量本身声明为 `mut`，再传 `&mut`：

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

> 注意三件套缺一不可：`let mut s`、调用处 `&mut s`、函数签名 `&mut String`。少一个都会报错。

## 借用规则（重点，背下来）

Rust 为了在编译期杜绝"数据竞争"（多个线程同时读写同一块内存搞坏数据），对借用定了两条铁律：

1. 在**同一时刻**，你要么只能有**多个不可变引用**（大家都看，随便），要么只能有**一个可变引用**（独占着改）。**不能同时既有可变又有不可变引用。**
2. 引用必须始终有效（不能出现"悬垂引用"）。

打个比方：

- 不可变引用 = 多人一起看同一份报纸，没问题。
- 可变引用 = 一个人拿笔在报纸上改稿，这时候别人不能同时看、更不能同时改。

```rust
let mut s = String::from("hello");

let r1 = &s;
let r2 = &s;          // ✅ 多个不可变引用，OK
println!("{} {}", r1, r2);
// r1、r2 到这里就不再使用了（作用域结束）

let r3 = &mut s;      // ✅ 之前的不可变引用已经不用了，现在可以独占
println!("{}", r3);
```

而下面这种"同时并存"就会报错：

```rust
let mut s = String::from("hello");

let r1 = &s;          // 不可变引用
let r2 = &mut s;      // ❌ 报错：cannot borrow `s` as mutable because
                      //     it is also borrowed as immutable
println!("{} {}", r1, r2);
```

> 好消息：引用的作用域到**最后一次使用**为止，不是到 `}` 为止。所以上面第一个例子里 `println!` 用完 `r1`、`r2` 之后，再开 `&mut s` 是完全合法的。编译器很聪明，不会乱拦你。

## 解引用 `*`

拿到引用后，如果要拿到它背后的**值本身**（而不是引用），用 `*` 解引用：

```rust
fn main() {
    let x = 10;
    let r = &x;            // r 是 &i32
    println!("{}", r);     // 能直接打印（自动解引用）
    println!("{}", *r);    // 手动解引用，得到 10

    let mut y = 5;
    let rr = &mut y;
    *rr += 1;              // 通过可变引用修改 y
    println!("{}", y);     // 6
}
```

> `s.len()` 这种方法调用，Rust 会自动帮你解引用，所以你不用到处写 `*`。知道 `*` 是"取背后的值"就够了。

## 悬垂引用（dangling reference）

如果一个值已经被释放了，却还留着一个引用指向它——这就是"悬垂引用"，C 里的经典崩溃。Rust 直接在编译期禁止：

```rust
fn main() {
    let reference_to_nothing = dangle();
}

fn dangle() -> &String {        // ❌ 返回一个 String 的引用
    let s = String::from("hello");
    s                           // 函数结束，s 被释放；
                                // 你却想返回它的引用，编译器不答应
}
```

正确做法是直接把 `String` 本身返回出去（所有权移交），而不是返回引用：

```rust
fn no_dangle() -> String {      // 返回 String，不是 &String
    let s = String::from("hello");
    s                            // 所有权移交给调用者，不会被提前释放
}
```

## 今日小结

- 引用不拿走所有权：`&s` 是不可变引用（能看不能改），`&mut s` 是可变引用（能改）。
- 函数参数写成 `&String` / `&mut String` 表示"借用"，函数结束不会释放数据。
- 借用规则：同一时刻，要么多个不可变引用，要么一个可变引用，**不能混着来**。
- 用 `*` 解引用拿到背后的值；方法调用会自动解引用。
- 不能返回已经释放变量的引用（悬垂引用）；要返回就直接返回值本身。

## 动手任务

1. 写一个函数 `fn calculate_length(s: &String) -> usize`，用不可变引用求字符串长度，验证传完引用后原变量还能用。
2. 写一个函数 `fn append_world(s: &mut String)`，通过可变引用给字符串末尾加上 `", world"`，在 `main` 里验证原字符串被改了。
3. 故意在同一作用域里同时写一个 `&s` 和一个 `&mut s` 并使用它们，读一遍编译器报的错，理解"不能同时有可变和不可变借用"。

**配合阅读**：[官方书·第4章 引用与借用](https://rustwiki.org/zh-CN/book/ch04-02-references-and-borrowing.html)
