# Day20 if let 与 while let

> 目标：学会在「只关心某一种情况」时用 `if let` 简写 match，并用 `while let` 循环处理不断取出值直到 None 的场景。

## if let：只关心一个分支

昨天的 `match` 很强大，但有个毛病：当你**只在意某一种情况**、其余情况都懒得处理时，写 match 就得配上一个 `_ => ()` 兜底，很啰嗦。

比如我只想看看 `Some(3)` 这个值：

```rust
let some_value: Option<u8> = Some(0u8);

// 用 match：要处理三个分支，但我只关心 Some(3)
match some_value {
    Some(3) => println!("是三！"),
    _ => (), // 其余情况什么都不做，纯陪跑
}
```

`if let` 就是为这种场景设计的简写：

```rust
let some_value: Option<u8> = Some(0u8);

// 等价于上面的 match，但短得多
if let Some(3) = some_value {
    println!("是三！");
}
```

读法：`if let 模式 = 值 { ... }` —— 如果这个值匹配上了左边的模式，就执行大括号里的代码。它同样能绑定值：

```rust
let maybe_num: Option<i32> = Some(42);

if let Some(n) = maybe_num {
    println!("拿到数字 {}", n); // n 被绑定为 42
}
```

## if let 配 else：处理另一种情况

`if let` 后面还能跟 `else`，相当于 match 里的 `_` 分支：

```rust
#[derive(Debug)]
enum UsState { Alabama, Alaska }
enum Coin { Penny, Quarter(UsState) }

let coin = Coin::Penny;
let mut count = 0;

if let Coin::Quarter(state) = coin {
    println!("这枚夸脱来自 {:?} 州！", state);
} else {
    count += 1; // 不是夸脱，计数器 +1
}
```

## 一个权衡：简写 vs 穷尽检查

| | `match` | `if let` |
|---|---|---|
| 代码量 | 必须列全所有分支 | 只写关心的那一个，短 |
| 安全检查 | 编译器强制穷尽，漏了会报错 | **不检查穷尽**，你忘了别的情况也没人拦 |
| 适合 | 要处理多种分支、需要编译器兜底 | 只在意一种情况，其余忽略 |

记住：`if let` 是用「放弃穷尽检查」换来了代码简洁。需要处理两三种以上情况时，老老实实用 `match`。

## while let：不断取值直到 None

还有一种常见循环：反复从一个集合里「弹出一个值」，只要有值就处理，直到变成 `None` 为止。这时候用 `while let`：

```rust
// 假设我们有个栈（Vec），不断从尾部弹出元素
let mut stack = vec![1, 2, 3];

// 只要 pop() 返回 Some(x)，就把 x 处理掉；返回 None 时循环结束
while let Some(top) = stack.pop() {
    println!("弹出了 {}", top);
}
// 输出 3, 2, 1（Vec::pop 从尾部弹出）
```

`Vec::pop()` 返回的就是 `Option<T>`：栈里有元素时返回 `Some(元素)`，空了返回 `None`。`while let` 的意思是「只要还能匹配上 `Some(top)`，就继续循环」。

如果不用 `while let`，你得这么写（明显啰嗦）：

```rust
let mut stack = vec![1, 2, 3];
loop {
    match stack.pop() {
        Some(top) => println!("弹出了 {}", top),
        None => break,
    }
}
```

## 动手看看

把下面这段贴进 `main` 跑一下，观察输出顺序：

```rust
fn main() {
    // if let 示例
    let score: Option<i32> = Some(100);
    if let Some(s) = score {
        println!("考试得了 {} 分", s);
    } else {
        println!("缺考");
    }

    // while let 示例
    let mut nums = vec![10, 20, 30];
    while let Some(n) = nums.pop() {
        println!("剩余 {}", n);
    }
}
```

运行下面命令看看输出：

```powershell
cargo run
```

## 今日小结

- `if let 模式 = 值 { ... }` 是「只关心一个分支」时 match 的简写，能绑定值
- `if let ... {} else {}` 的 `else` 等价于 match 的 `_` 分支
- 代价是失去 match 的穷尽性检查；多分支场景仍用 match
- `while let 模式 = 表达式 { ... }` 用于「不断取值直到 None」的循环，典型如 `while let Some(x) = v.pop()`

## 动手任务

1. 写一个 `Option<String>`，用 `if let Some(s)` 判断它是不是等于 `"hello"`，是就打印「匹配成功」。
2. 用 `while let Some(x) = vec![1,2,3,4,5].iter().next()`？不——改用一个 `Vec`，用 `pop()` 配合 `while let` 把所有元素倒序打印出来。
3. 故意写一个 `if let Some(n) = None { ... }`，观察它为什么不进分支（`None` 匹配不上 `Some(n)`）。

> 提示：`while let Some(x) = v.pop()` 里 `v` 必须是 `mut`，因为 `pop` 会修改 Vec。

**配合阅读**：[官方书·第6章 if let 简单控制流](https://rustwiki.org/zh-CN/book/ch06-03-if-let.html)
