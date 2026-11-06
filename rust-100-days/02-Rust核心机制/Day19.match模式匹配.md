# Day19 match 模式匹配

> **前置知识**：Day17 枚举（变体、`::`）、Day18 Option 与空值处理（`Some` / `None`、`{:?}`）
> **难度**：入门
> **预计阅读时间**：20 分钟
> **代码说明**：标 `📦` 的块是一个**完整程序**，整块贴进 `src/main.rs` 就能 `cargo run`。
> 标 `⚠️` 的块是我故意写错的，编译会报错，只给你看编译器怎么拦，别抄。
> 每个块都自带 `main`，别把两块摞进同一个文件，那样会报 `E0428`（名字重复）。换块的时候，把上一块整个替换掉。

---

## match 是什么

昨天手里有了枚举、有了 `Option`，但怎么「看它到底是哪个变体、再做对应处理」？答案是 **`match` 表达式**。

它像一个加强版的 `switch`：拿一个值，从上到下和各个**模式**比对，命中哪个就执行哪个分支的代码，然后整个 `match` 结束。这里的**模式（pattern）**指的是「值的形状」，比如 `Coin::Penny` 是具体某个变体，`Some(i)` 则是「是 `Some` 就行，里面装的东西顺便管它叫 `i`」。

和 C 的 `switch` 不同，Rust 的 `match` **必须穷尽**：所有可能都得被覆盖到，少一个就编译报错。

📦 本块能单独跑：

```rust
enum Coin {
    Penny,    // 1 美分
    Nickel,   // 5 美分
    Dime,     // 10 美分
    Quarter,  // 25 美分
}

fn value_in_cents(coin: Coin) -> u8 {
    match coin {
        // 命中这一支就执行大括号里的东西，最后一行的 1 是这一支的值
        Coin::Penny => {
            println!("幸运便士！");
            1
        }
        Coin::Nickel => 5,
        Coin::Dime => 10,
        Coin::Quarter => 25,
    }
}

fn main() {
    println!("一分钱值 {} 美分", value_in_cents(Coin::Penny));
    println!("五分钱值 {} 美分", value_in_cents(Coin::Nickel));
    println!("一角钱值 {} 美分", value_in_cents(Coin::Dime));
    println!("两角五分值 {} 美分", value_in_cents(Coin::Quarter));
}
```

几个要点：

- 每个分支长这样：`模式 => 对应代码`。箭头左边是模式，右边是命中后执行的表达式。每组用逗号隔开。
- 分支里要写多行逻辑时，用 `{}` 包起来。这时**最后一行（不加分号）就是这一支的值**，跟函数体最后一行是返回值一个道理。`{}` 后面那个逗号可加可不加。
- 整个 `match` 是个表达式，每一支的值就是它的结果，所以所有分支的值必须是同一个类型，这里都是整数。

## 模式绑定值：把枚举里的数据掏出来

`match` 最好用的地方是：它能把变体携带的数据**绑定**到变量上，直接在分支里用。

📦 本块能单独跑：

```rust
#[derive(Debug)]     // 下面要用 {:?} 打印州名
enum UsState {
    Alabama,
    Alaska,
    // ... 其他州先省略
}

enum Coin {
    Penny,
    Quarter(UsState),   // 25 美分硬币额外记着它来自哪个州
}

fn value_in_cents(coin: Coin) -> u8 {
    match coin {
        Coin::Penny => 1,
        Coin::Quarter(state) => {          // state 绑定硬币里带的那个 UsState
            println!("这枚夸脱来自 {:?} 州", state);
            25
        }
    }
}

fn main() {
    println!("{} 美分", value_in_cents(Coin::Penny));
    println!("{} 美分", value_in_cents(Coin::Quarter(UsState::Alaska)));
    println!("{} 美分", value_in_cents(Coin::Quarter(UsState::Alabama)));
}
```

`Coin::Quarter(state)` 里的 `state` 不是你提前声明的变量，是 match 现场替你绑的：命中这一支时，它就等于硬币里存的那个 `UsState`。名字随便起，写成 `st`、`s` 都行。

## 匹配 Option

Day18 学的 `Option` 用 `match` 拆起来很自然：

📦 本块能单独跑：

```rust
fn plus_one(x: Option<i32>) -> Option<i32> {
    match x {
        None => None,             // 没值，我也返回没值
        Some(i) => Some(i + 1),   // 有值 i，就返回 i + 1
    }
}

fn main() {
    let five = Some(5);
    let six = plus_one(five);      // Some(6)
    let none = plus_one(None);     // None
    println!("{:?}, {:?}", six, none);
}
```

分支顺序无所谓，`None` 写在前面、`Some` 写在前面都能编。把 `None => None,` 那一行删掉试试，编译器会直接拦你，报错原文就在下面。

### ⚠️ 踩坑：match 少写一个分支，报 `E0004`

漏掉一个变体是 `match` 最常见的错。这个坑我踩过，`plus_one` 里忘了写 `None` 那一支：

⚠️ 本块编译不过，贴出来是给你看编译器怎么拦：

```rust
fn plus_one(x: Option<i32>) -> Option<i32> {
    match x {
        Some(i) => Some(i + 1),
    }
}

fn main() {
    println!("{:?}", plus_one(Some(5)));
}
```

编译器原话：

```
error[E0004]: non-exhaustive patterns: `None` not covered
 --> src\main.rs:2:11
  |
2 |     match x {
  |           ^ pattern `None` not covered
  |
  = note: the matched value is of type `Option<i32>`
help: ensure that all possible cases are being handled by adding a match arm with a wildcard pattern or an explicit pattern as shown
  |
3 ~         Some(i) => Some(i + 1),
4 ~         None => todo!(),
  |
```

`non-exhaustive` 的意思就是「覆盖不全」。编译器不光点出缺的是 `None`，连补上去的模板都给你写好了。`Option` 只有两支，好补；枚举变体多起来之后，漏一个照样会被它揪出来。看到 `E0004`，回去把变体列表和 `match` 的分支对一遍。

## 穷尽性与兜底通配

有时候你不想、也不可能把每个值都列出来。比如掷骰子，只有掷出 3 和 7 有特殊动作，其他点数做同一件事。这时用**通配**兜底：

📦 本块能单独跑：

```rust
fn main() {
    let dice_roll = 9;

    match dice_roll {
        3 => println!("戴帽子"),
        7 => println!("摘帽子"),
        other => println!("移动 {} 步", other),   // 其余所有点数都走这
    }
}
```

这里有两种兜底写法：

| 写法 | 含义 |
|---|---|
| `other => ...` | 一个普通变量名，捕获剩余的值，并且**能在分支里用**它 |
| `_ => ...` | 特殊占位符，匹配任意值但**不绑定**，不会产生未使用变量警告 |

不关心那个值的时候用 `_`：

📦 本块能单独跑：

```rust
fn main() {
    let dice_roll = 9;

    match dice_roll {
        3 => println!("戴帽子"),
        7 => println!("摘帽子"),
        _ => println!("普通一步"),   // 不关心是几点
    }
}
```

还有个常见写法 `_ => ()`。`()` 是「单元类型」，值就是「什么都没有」，这里表示「其他情况我知道，但懒得处理」。

> 通配分支必须放最后。Rust 从上往下匹配，它放在前面会把后面的分支全挡掉，编译器也会提醒你后面的分支永远匹配不到。

## match 是表达式，能赋值

既然 `match` 是表达式，它的结果可以直接赋给变量：

📦 本块能单独跑：

```rust
fn main() {
    let maybe: Option<i32> = Some(7);

    // 整个 match 的值赋给 squared
    let squared = match maybe {
        Some(n) => {
            println!("拿到 {}", n);
            n * n
        }
        None => 0,
    };

    println!("结果是 {}", squared);
}
```

注意最后那个 `};`：`match` 先算出结果，再赋给 `squared`。所有分支的值必须是同一个类型，这里都是整数，写成 `None => "没有"` 就会被编译器拦下来。

## 今日小结

- `match 值 { 模式 => 表达式, ... }` 从上往下匹配，命中即执行并结束
- 分支可以绑定变体携带的数据，比如 `Coin::Quarter(state) => ...`
- `match` 必须穷尽所有可能，漏了 `None` 之类会报 `E0004`，这是它的安全保证
- 兜底用 `其他变量名`（能拿到剩余的值）或 `_`（不绑定），且必须放最后
- `match` 本身是表达式，结果可以赋给变量，也可以直接当函数的返回值

## 动手任务

**1.** 定义一个枚举 `Direction { Up, Down, Left, Right }`，写函数 `fn dir_name(d: Direction) -> &'static str`，用 `match` 返回 `"上"` / `"下"` / `"左"` / `"右"`。

**2.** 给 `Option<i32>` 写一个 `match`：`Some(n)` 时打印它的平方，`None` 时打印「没有数字」。用 `Some(6)` 和 `None` 各调一次。

**3.** 在第 2 题那个 `match` 里删掉 `None` 分支，编译一次，对着上面踩坑框里的 `E0004` 读一遍报错，再把分支加回去。

> 提示 1：`&'static str` 里的 `'static` 表示这个借用能活到程序结束，正好适合返回字符串字面量。生命周期 Day28 才讲，现在照写就行。
> 提示 2：`Some(i) =>` 里的 `i` 是被绑定出来的值，可以直接参与运算，比如 `i * i`。

**配合阅读**：[官方书·第6章 match 控制流](https://rustwiki.org/zh-CN/book/ch06-02-match.html)
