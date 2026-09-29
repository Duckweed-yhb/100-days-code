# Day08 控制流 if

> 目标：用 `if / else if / else` 做分支判断，并理解 Rust 的 `if` 是一个能返回值的表达式。

## if 是做什么的

程序不一定是从上到下一路走到底的。很多时候你要"看情况办事"：成绩及格了就打印"通过"，否则打印"补考"。这种"条件成立才走某条路"的逻辑，就靠 `if`。

## 最基本的 if / else

```rust
fn main() {
    let score = 76;

    if score >= 60 {
        println!("恭喜，通过了！");
    } else {
        println!("很遗憾，需要补考");
    }
}
```

Rust 的 `if` 有两个和 C/Java 不一样的地方：

1. **判断条件外面不用写括号**。`if score >= 60` 直接写，加括号也没错但属于多余。
2. **条件必须是 `bool`**。Rust 不会像某些语言那样把数字 `0`、空字符串自动当成"假"。下面这样写会直接报错：

   ```rust
   if 1 {        // ❌ 报错：expected `bool`, found integer
       println!("不会执行");
   }
   ```

   必须老老实实地写 `if 1 == 1 { ... }`。

## 多分支 else if

超过两种情况时，用 `else if` 依次判断：

```rust
fn main() {
    let score = 85;

    if score >= 90 {
        println!("优秀");
    } else if score >= 80 {
        println!("良好");
    } else if score >= 60 {
        println!("及格");
    } else {
        println!("不及格");
    }
}
```

执行顺序：从上往下判断，**命中第一个成立的分支后就不再往下走**；全都不成立才走最后的 `else`。

> `else if` 写多了会显得啰嗦。Rust 后面会学 `match` 表达式，那种"多分支匹配"的场景用 `match` 更优雅，现在先用 `if`。

## if 是一个表达式

这是 Rust 很特别的一点：**`if` 会产生一个值**。也就是说，整个 `if ... else ...` 可以当成一个值赋给变量。

```rust
fn main() {
    let age = 20;

    let category = if age >= 18 {
        "成年人"      // 注意：没有分号，这是个表达式值
    } else {
        "未成年人"
    };               // 整个 if 语句结束，这里有分号

    println!("{}", category);
}
```

类比 Rust 函数的返回值：每个分支块里最后一个**不带分号**的表达式，就是这个分支返回的值。

### 两个分支返回的类型必须一致

既然 `if` 是表达式、要返回一个值，那么**所有分支产出的类型必须相同**，否则编译器报错：

```rust
let x = if true {
    5            // i32
} else {
    "six"        // ❌ 字符串，和上面的 i32 类型对不上
};
```

```rust
// ✅ 正确：两边都是 i32
let x = if true { 5 } else { 6 };
```

## 用 let + if 优雅地写"二选一"

利用 `if` 是表达式这个特性，很多"根据条件取不同值"的代码可以写得非常简洁。比如求两个数的较大值：

```rust
fn main() {
    let a = 30;
    let b = 50;
    let bigger = if a > b { a } else { b };
    println!("较大的数是 {}", bigger);
}
```

运行下面命令看看输出：

```powershell
cargo run
```

## 今日小结

- `if 条件 { ... } else if 条件 { ... } else { ... }`，条件外面不用括号，但**必须是 `bool`**。
- 判断从上往下，命中第一个就停；都不命中走 `else`。
- `if` 是表达式，可以返回值；把它赋给变量时，**各分支类型必须一致**。
- 分支块里靠"不带分号的最后一个表达式"产出值。

## 动手任务

1. 写程序：给定一个成绩 `score`（自己设值），用 `if/else if/else` 打印"优秀/良好/及格/不及格"。
2. 用 `let 变量 = if ... else ...` 表达式，求三个数里的最大值（提示：可以嵌套两个 `if`）。
3. 故意写一段 `if 0 { ... }`（条件是数字），看看编译器怎么报错，体会"条件必须是 bool"。

**配合阅读**：[官方书·第3章 控制流（if 表达式）](https://rustwiki.org/zh-CN/book/ch03-05-control-flow.html)
