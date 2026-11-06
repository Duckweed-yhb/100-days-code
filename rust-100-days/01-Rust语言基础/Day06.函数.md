# Day06 函数

> **前置知识**：Day03 变量与可变性、Day04 基本数据类型、Day05 运算符
> **难度**：入门
> **预计阅读时间**：20 分钟
> **目标**：学会定义和调用函数，理解 Rust 里“语句”和“表达式”的区别，掌握返回值怎么写。
> **代码说明**：本章代码都放在**同一个** `main.rs` 里，按顺序往下加就能跑。
> 每块前面标了 `📦`（能单独跑）、`🧩`（依赖上面的块）或 `⚠️`（只给你看，故意编不过，别照抄）。

---

## 函数是什么

函数就是给一段代码起个名字，打包起来反复用。你已经见过 `main` 函数，今天自己造几个。

## 定义和调用

用 `fn` 关键字定义函数（`fn` 是 function 的缩写），写法是 `fn 名字(参数) { 函数体 }`。先看个没有参数、也不返回东西的例子：

📦 本块能单独跑：

```rust
fn main() {
    greet();                 // 调用函数
    greet();
}

// 定义一个打招呼的函数
fn greet() {
    println!("你好，Rust！");
}
```

要点：

- **调用**要写函数名加括号 `greet();`。括号是“执行”的意思，只写 `greet;` 不会执行。
- **定义位置无所谓**：`greet` 写在 `main` 上面或下面都行，Rust 不挑顺序。
- 函数名习惯用小写字母加下划线（snake_case，蛇形命名），比如 `calculate_price`。

## 参数必须标类型

这是 Rust 和很多语言不一样的地方：**每个参数都必须写明类型**。参数名和类型之间用 `:` 隔开，`n: i32` 读作“n 的类型是 i32”。

📦 本块能单独跑：

```rust
fn main() {
    print_number(5);
}

fn print_number(n: i32) {      // 参数 n 的类型是 i32，不能省
    println!("数字是 {}", n);
}
```

`println!` 里的 `{}` 是占位符，会被后面跟的参数按顺序填上。

多个参数用逗号隔开：

📦 本块能单独跑：

```rust
fn main() {
    add(3, 5);
}

fn add(a: i32, b: i32) {
    println!("{} + {} = {}", a, b, a + b);
}
```

> 为什么这么严？函数是独立的一段代码，Rust 要求它的输入输出类型都白纸黑字写清楚，读代码时一眼就知道它吃什么、吐什么。

### 调用时参数个数要对

📦 本块能单独跑：

```rust
fn main() {
    print_num(3);          // ✅ 传 1 个，正好
}

fn print_num(n: i32) {
    println!("{}", n);
}
```

少传或者多传都会报错，错误码是 `E0061`：

⚠️ 本块故意编不过，只给你看两种错法，别照抄：

```rust
fn main() {
    print_num();           // ❌ 缺参数
    print_num(1, 2);       // ❌ 多参数
}

fn print_num(n: i32) {
    println!("{}", n);
}
```

编译器的原话：

```
error[E0061]: this function takes 1 argument but 0 arguments were supplied
 --> src\main.rs:2:5
  |
2 |     print_num();
  |     ^^^^^^^^^-- argument #1 of type `i32` is missing
  |
note: function defined here
 --> src\main.rs:6:4
  |
6 | fn print_num(n: i32) {
  |    ^^^^^^^^^ ------

error[E0061]: this function takes 1 argument but 2 arguments were supplied
 --> src\main.rs:3:5
  |
3 |     print_num(1, 2);
  |     ^^^^^^^^^    - unexpected argument #2 of type `{integer}`
```

报错信息里会写清楚“少了第几个参数”“多出来的是哪一个”，照着改就行。

## 返回值

函数可以把结果交给调用者。在参数列表后面用 `-> 类型` 声明返回类型，`-> i32` 读作“返回一个 i32”。

📦 本块能单独跑：

```rust
fn main() {
    let result = add(3, 5);
    println!("3 + 5 = {}", result);
}

fn add(a: i32, b: i32) -> i32 {   // -> i32 表示返回一个 i32
    a + b                          // 注意：行尾没有分号
}
```

这里有条容易栽跟头的规则：**函数的返回值，就是函数体里最后一个不带分号的表达式的值。**

## 语句和表达式

- **语句（statement）**：做一件事，**以分号结尾**，不产生值。比如 `let x = 5;`、`println!("hi");`。
- **表达式（expression）**：**会产生一个值**的东西，后面**不写分号**。比如 `5 + 3`、`a + b`。

📦 本块能单独跑：

```rust
fn main() {
    println!("{}", square(4));    // 输出 16
}

fn square(num: i32) -> i32 {
    num * num        // ✅ 没有分号，这个值就是返回值
}
```

把最后一行的分号补上，就编不过了：

⚠️ 本块故意编不过（`num * num;` 加了分号），别照抄：

```rust
fn main() {
    println!("{}", square(4));
}

fn square(num: i32) -> i32 {
    num * num;
}
```

编译器的原话：

```
error[E0308]: mismatched types
 --> src\main.rs:5:24
  |
5 | fn square(num: i32) -> i32 {
  |    ------              ^^^ expected `i32`, found `()`
  |    |
  |    implicitly returns `()` as its body has no tail or `return` expression
6 |     num * num;
  |              - help: remove this semicolon to return this value
```

加了分号之后，`num * num;` 变成一条语句，不再产出值，函数等于返回了空元组 `()`，和声明的 `i32` 对不上。编译器最后一行直接告诉你 `remove this semicolon`。

> 记法：**加分号 = “这件事说完了”；不加分号 = “把这个值交出去”。** 函数要返回值，就把最后一行的分号去掉。

### 也可以用 `return` 提前返回

想在函数中间就返回（比如出错时），用 `return`：

📦 本块能单独跑：

```rust
fn main() {
    println!("12 / 3 = {}", divide(12, 3));
    println!("12 / 0 = {}", divide(12, 0));
}

fn divide(a: i32, b: i32) -> i32 {
    if b == 0 {
        println!("除数不能为 0");
        return 0;            // 提前返回
    }
    a / b                    // 正常走到最后，返回 a / b
}
```

多数情况下 Rust 风格倾向于靠最后一个表达式返回，少写 `return`。

## 没有返回值的函数

不返回东西的函数就不写 `-> 返回类型`。它实际上返回的是空元组 `()`（读作 unit），作用类似 C 语言里的 `void`。

📦 本块能单独跑：

```rust
fn main() {
    say_hello();
}

fn say_hello() {          // 没有 ->，返回类型就是 ()
    println!("hello");
}
```

`()` 可以理解成“什么都没有”。前面那些没写 `->` 的 `main` 函数，返回的也是它。

## 串起来的一个例子

📦 本块能单独跑：

```rust
fn main() {
    let price = 80;
    let final_price = sale_price(price);
    println!("原价 {}，折后 {}", price, final_price);
}

// 偶数价格减 10，奇数价格减 3
fn sale_price(price: i32) -> i32 {
    if is_even(price) {
        price - 10        // 表达式，返回
    } else {
        price - 3         // 表达式，返回
    }
}

fn is_even(num: i32) -> bool {
    num % 2 == 0          // 比较表达式，结果是 bool
}
```

这里一个 `return` 都没写。两个分支各自把最后一行留成不带分号的表达式，函数就返回了它。

运行看看输出：

```powershell
cargo run
```

## 今日小结

- 用 `fn 名(参数: 类型) -> 返回类型 { ... }` 定义函数，函数名用 snake_case。
- **每个参数都必须标注类型**，调用时个数要对，错了报 `E0061`。
- 返回值用 `-> 类型` 声明；函数体最后一个**不带分号**的表达式就是返回值。
- 语句以 `;` 结尾、不产生值；表达式不写分号、会产生值。
- 不写 `->` 的函数返回空元组 `()`；提前返回用 `return`。

## 动手任务

1. 写一个函数 `fn square(n: i32) -> i32`，返回 `n` 的平方，在 `main` 里调用并打印 `square(7)`。
2. 写一个函数 `fn max(a: i32, b: i32) -> i32`，返回两个数中较大的那个（提示：用 `if` 表达式，Day08 细讲）。
3. 故意给 `square` 函数体最后一行加上分号变成 `n * n;`，自己敲一遍看编译器报什么。上面那段 `E0308` 的原话你已经见过，亲手撞一次体会更深。

**配合阅读**：[官方书 · 第3章 函数如何运作](https://rustwiki.org/zh-CN/book/ch03-03-how-functions-work.html)
