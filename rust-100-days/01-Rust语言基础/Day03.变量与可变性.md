# Day03 变量与可变性

> **前置知识**：Day02（`fn main`、`println!`、`cargo run`）
> **难度**：入门
> **预计阅读时间**：15 分钟
> **代码说明**：本章每块代码都是一个完整的 `main.rs`，一块一块替换进去跑就行，不用叠在一起。
> 每块前面标了 `📦`（能单独跑）或 `⚠️`（故意编不过的示例，用来看报错，别照着抄）。

> 目标：学会用 `let` 声明变量，理解 Rust「默认不可变」的设计，掌握 `mut`、常量和变量遮蔽。

---

## 用 `let` 声明变量

在 Rust 里，**创建一个变量必须用 `let` 关键字**。`let` 后面跟变量名，等号后面跟值：

📦 本块能单独跑：

```rust
fn main() {
    let x = 5;        // 声明变量 x，绑定值 5
    println!("x = {}", x);
}
```

这和很多语言不一样：你不能上来就写 `x = 5;`，Rust 会报错，它要求先「声明」再使用。这个设计让每个变量的来源都在明处。

> Rust 是**静态强类型**语言：每个变量在编译时就有确定类型。「静态」指类型在编译期定下来，「强类型」指不同类型不会悄悄互转。不过大多数时候编译器能根据右边的值**自动推断**类型，所以一开始不用手写类型。

## Rust 变量默认不可变

这是 Rust 最有个性的一点：**用 `let` 声明的变量，默认是不可变（immutable）的**，绑定了值就不能再改。

⚠️ 本块故意编不过（演示不可变变量被二次赋值，`E0384`）：

```rust
fn main() {
    let x = 5;
    x = 6;              // ❌ 编译错误：cannot assign twice to immutable variable
    println!("{}", x);
}
```

编译器原话是这样：

```
error[E0384]: cannot assign twice to immutable variable `x`
  |
2 |     let x = 5;
  |         - first assignment to `x`
3 |     x = 6;
  |     ^^^^^ cannot assign twice to immutable variable
  |
help: consider making this binding mutable
  |
2 |     let mut x = 5;
  |         +++
```

它连改法都给你了：把 `let x` 换成 `let mut x`。`E0384` 这个编号记不记都行，认识「cannot assign twice to immutable variable」这句话就够。

为什么默认不可变？因为不可变的值好推理：值定下来以后，你不用提心吊胆看它在别的地方被谁改掉。

## 想改值就加 `mut`

确实需要让变量变化时，在 `let` 后面加 `mut`（mutable，可变的）：

📦 本块能单独跑：

```rust
fn main() {
    let mut x = 5;
    println!("改之前：{}", x);
    x = 6;              // ✅ 加了 mut，就可以重新赋值
    println!("改之后：{}", x);
}
```

> 原则：**能不用 `mut` 就不用**。需要改的时候再加，这样编译器能帮你抓住「不该改却改了」的 bug。

## 变量使用前必须有值

Rust 不允许使用一个还没赋值的变量：

⚠️ 本块故意编不过（演示未初始化就使用，`E0381`）：

```rust
fn main() {
    let x: i32;        // 只声明了类型，没给值
    println!("{}", x); // ❌ 错误：使用了未初始化的变量
}
```

报错是：

```
error[E0381]: used binding `x` isn't initialized
  |
2 |     let x: i32;
  |         - binding declared here but left uninitialized
3 |     println!("{}", x);
  |                    ^ `x` used here but it isn't initialized
  |
help: consider assigning a value
```

直接在声明时给值最省事：`let x: i32 = 0;`。这种「未初始化就用」的错误，在 C 里可能读到一块随机内存，Rust 在编译期就拦下来。

## 变量遮蔽（shadowing）

Rust 允许你用 `let` **重新声明一个同名变量**，新变量把旧的「遮蔽（shadow）」掉，之后 `x` 指的都是新的那个：

📦 本块能单独跑：

```rust
fn main() {
    let x = 5;
    let x = x + 1;      // 用 let 重新声明，右边的 x 还是旧的 5，加完是 6
    let x = x * 2;      // 再遮蔽一次，6 * 2
    println!("x = {}", x);  // 输出 12
}
```

第二行右边那个 `x` 用的是旧值，所以是 `5 + 1`。同一个名字在同一个作用域里能反复绑定，这在 Rust 里是正常写法，不算错误。

遮蔽还能换类型，`mut` 不行：

📦 本块能单独跑：

```rust
fn main() {
    let spaces = "    ";        // spaces 是字符串（&str）
    let spaces = spaces.len();  // 遮蔽成一个数字（空格个数，类型是 usize）
    println!("空格数：{}", spaces);
}
```

而用 `mut` 想换类型就会编不过：

⚠️ 本块故意编不过（演示 `mut` 变量不能换类型，`E0308`）：

```rust
fn main() {
    let mut spaces = "    ";
    spaces = spaces.len();   // ❌ 类型不匹配：&str 不能直接变成 usize
}
```

编译器原话：

```
error[E0308]: mismatched types
  |
2 |     let mut spaces = "    ";
  |                      ------ expected due to this value
3 |     spaces = spaces.len();
  |              ^^^^^^^^^^^^ expected `&str`, found `usize`
```

`&str` 是字符串切片类型，Day04 讲 `char` 时会提到它和 `char` 的区别，Day13 正式讲字符串。这里只要看懂 `expected X, found Y` 这个句式：`mut` 只允许换值，不允许换类型。

### `mut` 和遮蔽的区别

| | `let mut x` | `let x = ...`（遮蔽） |
|---|---|---|
| 本质 | 同一个变量，改它的值 | **新建**一个同名变量 |
| 能否换类型 | 不能，类型必须一致 | 能，新旧类型随意 |
| 是否需要 `let` | 只在第一次写 | 每次遮蔽都要写 `let` |

## 常量 `const`

**常量（constant）**用 `const` 声明，而不是 `let`。常量可以在函数外面定义，整个程序都能用：

📦 本块能单独跑：

```rust
const MAX_POINTS: i32 = 100_000;

fn main() {
    println!("上限是 {}", MAX_POINTS);
}
```

它和变量的区别：

| 特性 | `let` 变量 | `const` 常量 |
|---|---|---|
| 可变性 | 默认不可变，可加 `mut` | **永远不可变**，不能加 `mut` |
| 类型标注 | 通常可省略（编译器推断） | **必须显式标注类型** |
| 命名习惯 | 随便 | 全大写 + 下划线，如 `MAX_POINTS` |
| 能在哪用 | 函数里等局部作用域 | 可以在任何作用域，包括全局 |

> 常量声明里 `: i32` 这个类型标注是**必须**的，省了会报错。另外 Rust 允许用下划线 `_` 分隔数字，`100_000` 就是 `100000`，纯粹为了好看。

## 类型标注怎么写

编译器推不出类型时，你要手动标注。写法是变量名后面加冒号和类型：

📦 本块能单独跑：

```rust
fn main() {
    let guess: u32 = "42".parse().expect("不是数字！");
    println!("猜的数字是 {}", guess);
}
```

这里有两个没见过的写法：

- `: u32` 是**类型标注**，告诉编译器「这个变量是 32 位无符号整数」。
- `.parse()` 是把字符串转成数字的方法。`"42"` 这种字符串可以转成好几种整数类型（`u32`、`i64` 都行），编译器不知道你要哪个，所以**必须**靠 `: u32` 指定。它返回一个 `Result`，可能成功也可能失败，`.expect("不是数字！")` 表示「成功就取出值，失败就带着这句话崩掉」。`Result` 和错误处理 Day25 才讲，今天照抄这一行就行。

常见类型名（Day04 详讲）：`i32`（默认整数）、`f64`（默认小数）、`bool`、`char`。

## 今日小结

- 创建变量用 `let`；Rust 变量**默认不可变**，需要改时加 `mut`。
- 变量在使用前必须赋值，未初始化就用会编译报错。
- 可以用 `let` 重复声明同名变量来**遮蔽**它，遮蔽还能换类型，`mut` 不能。
- 常量用 `const 名字: 类型 = 值;` 声明，全大写命名，永远不可变、必须标注类型。
- 编译器推断不出类型时，手动写 `let x: 类型 = 值;`。

## 动手任务

1. 写一个程序，把 `count` 从 `0` 弄到 `3`，每一步都 `println!` 出来。要求分两步做：先用 `let count = 1;` 遮蔽 `let count = 0;`，再用 `let mut count = count;` 重新绑成可变的那个，然后用 `count += 1;` 两次加到 3。写完对照一下：如果没有最后那句 `let mut`，`count += 1` 会报什么错？
2. 定义一个常量 `const SPEED_LIMIT: i32 = 120;`，在 `main` 里打印它；然后把 `const` 改成 `const mut`，编译。会看到 `error: const globals cannot be mutable`，意思是常量不能是可变的。
3. 故意写一段「未初始化就使用变量」的代码，把编译器的报错读懂，再改成正确写法。

---

**配合阅读**：[官方书·第3章 变量与可变性](https://rustwiki.org/zh-CN/book/ch03-01-variables-and-mutability.html)
