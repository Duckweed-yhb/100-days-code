# Day19 match 模式匹配

> 目标：学会用 `match` 把一个值和各种模式做比较，并强制自己把所有可能的情况都处理干净。

## match 是什么

昨天我们手里有了枚举、有了 `Option`，但怎么「看它到底是哪个变体、再做对应处理」？答案是 **`match` 表达式**。

你可以把它想象成一个超级加强版的 `switch`：拿一个值，从上到下和各个**模式**比对，命中哪个就执行哪个分支的代码，然后自动结束。和 C 语言的 `switch` 不同，Rust 的 `match **必须穷尽**——所有可能都得被覆盖到，否则编译报错。

```rust
enum Coin {
    Penny,
    Nickel,
    Dime,
    Quarter,
}

fn value_in_cents(coin: Coin) -> u8 {
    match coin {
        Coin::Penny => 1,
        Coin::Nickel => 5,
        Coin::Dime => 10,
        Coin::Quarter => 25,
    }
}

fn main() {
    println!("一分钱值 {} 美分", value_in_cents(Coin::Penny)); // 1
}
```

每个分支长这样：`模式 => 对应代码`。箭头 `=>` 左边是模式，右边是命中后执行的表达式。

## 多行分支用大括号

分支里要写多行逻辑时，用 `{}` 包起来，末尾加逗号：

```rust
fn value_in_cents(coin: Coin) -> u8 {
    match coin {
        Coin::Penny => {
            println!("幸运便士！");
            1
        }
        Coin::Nickel => 5,
        Coin::Dime => 10,
        Coin::Quarter => 25,
    }
}
```

注意 `match` 是个**表达式**，每个分支的值会作为整个 `match` 的返回值（这里就是那个整数）。

## 模式绑定值：把枚举里的数据掏出来

`match` 最强大的地方是：它能把枚举变体携带的数据**绑定**到变量上，直接在分支里用。

```rust
#[derive(Debug)]
enum UsState {
    Alabama,
    Alaska,
    // ... 其他州
}

enum Coin {
    Penny,
    Quarter(UsState), // 25 美分币额外附带它是哪个州的
}

fn value_in_cents(coin: Coin) -> u8 {
    match coin {
        Coin::Penny => 1,
        Coin::Quarter(state) => {        // state 自动绑定到附带的 UsState
            println!("这枚夸脱来自 {:?} 州！", state);
            25
        }
    }
}
```

这里 `Coin::Quarter(state)` 里的 `state` 不是你提前声明的变量，而是 **match 现场帮你绑定**的——命中这个分支时，它就等于硬币里存的那个 `UsState`。

## 匹配 Option

昨天学的 `Option` 用 `match` 拆起来非常自然：

```rust
fn plus_one(x: Option<i32>) -> Option<i32> {
    match x {
        None => None,        // 没值，我也返回没值
        Some(i) => Some(i + 1), // 有值 i，就返回 i+1
    }
}

fn main() {
    let five = Some(5);
    let six = plus_one(five);      // Some(6)
    let none = plus_one(None);     // None
    println!("{:?}, {:?}", six, none);
}
```

试试把 `None => None,` 那行删掉会怎样？编译器会报错 `non-exhaustive patterns`（模式未穷尽）。这就是 Rust 在逼你：**遇到 Option，你不可能忘了处理 None。**

## 穷尽性与兜底通配

有时候你不想、也不可能把每个值都列出来。比如掷骰子，只有掷出 3 和 7 有特殊动作，其他点数都做同一件事。这时用**通配符**兜底：

```rust
let dice_roll = 9;

match dice_roll {
    3 => println!("戴帽子"),
    7 => println!("摘帽子"),
    other => println!("移动 {} 步", other), // 其余所有点数都走这
}
```

这里有两种兜底写法：

| 写法 | 含义 |
|---|---|
| `other => ...` | 一个普通变量名，捕获剩余的值并**能在分支里用**它 |
| `_ => ...` | 特殊占位符，匹配任意值但**不绑定**（不会产生未使用变量警告） |

当你不关心那个剩余的值时，用 `_`：

```rust
let dice_roll = 9;
match dice_roll {
    3 => println!("戴帽子"),
    7 => println!("摘帽子"),
    _ => println!("普通一步"), // 不关心是几点
}
```

还有个常见写法 `_ => ()`，`()` 表示「什么都不做」，意思是「其他情况我都知道了，但懒得处理」。

> 注意：通配分支必须放最后，因为 Rust 是从上往下匹配，放前面会让后面的分支永远命中不了。

## match 是表达式，能赋值

既然 `match` 是表达式，它的结果可以直接赋给变量：

```rust
let score = match Coin::Quarter(UsState::Alaska) {
    Coin::Penny => 1,
    Coin::Quarter(state) => {
        println!("来自 {:?}", state);
        25
    }
    _ => 0,
};
```

## 今日小结

- `match 值 { 模式 => 表达式, ... }` 从上往下匹配，命中即执行并结束
- 分支可绑定变体携带的数据，如 `Coin::Quarter(state) => ...`
- `match` 必须穷尽所有可能，漏了 `None` 之类会编译报错——这是它的安全保证
- 兜底用 `其他变量名`（能拿到剩余值）或 `_`（不绑定），且必须放最后
- `match` 本身是表达式，其结果可以赋给变量

## 动手任务

1. 定义一个枚举 `Direction { Up, Down, Left, Right }`，写函数 `dir_name(d: Direction) -> &'static str`，用 match 返回 `"上"/"下"/"左"/"右"`。
2. 给 `Option<i32>` 写一个 match：`Some(n)` 时打印它的平方，`None` 时打印「没有数字」。
3. 把 `Option` 的 match 里 `None` 分支删掉，观察编译器报的 `non-exhaustive` 错误，体会穷尽性检查。

> 提示：`Some(i) =>` 里的 `i` 就是被绑定出来的值，可以直接参与运算 `i * i`。

**配合阅读**：[官方书·第6章 match 控制流](https://rustwiki.org/zh-CN/book/ch06-02-match.html)
