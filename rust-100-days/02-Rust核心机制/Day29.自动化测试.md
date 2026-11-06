# Day29 自动化测试

> **前置知识**：Day19 match 模式匹配、Day25 错误处理、Day27 Trait
> **难度**：进阶
> **预计阅读时间**：25 分钟
> **代码说明**：本章代码写在**库项目**（`cargo new --lib`）的 `src/lib.rs` 里，用 `cargo test` 执行。它没有 `main` 函数，单用 `rustc` 编不过，这是正常的。
> 每块前面标了 `📦`（能独立跑）或 `🧩`（要接在上一块或同一个文件里）。

目标：学会用 Rust 内置的测试框架写单元测试，用 `cargo test` 一键验证代码对不对。

---

## Rust 自带测试框架

别的语言往往要装一堆测试库。Rust 把测试能力直接做进了语言和 Cargo：给一个函数加 `#[test]` 属性，跑 `cargo test`，它就自动找到并执行。

核心观念很简单：**测试就是一个运行结束会判定成败的函数。函数里 panic 了就算失败，没 panic 就算通过。**

`#[test]` 是**属性（attribute）**，形式是 `#[...]`，写在被修饰的东西上面一行。`#[derive(Debug)]` 也是属性，Day17 用过。

---

## 第一个测试

新建一个库包最方便：

```powershell
cargo new --lib my_calc
cd my_calc
```

把 `src/lib.rs` 改成：

🧩 本块是本章第一个块（`src/lib.rs` 的完整内容，靠 `cargo test` 跑；没有 `main`，单用 `rustc` 编不过）：

```rust
// 被测代码
pub fn add_two(a: i32) -> i32 {
    a + 2
}

// 测试模块：惯例放在文件底部
#[cfg(test)]
mod tests {
    use super::*; // 把父模块的东西引进来

    #[test]
    fn it_adds_two() {
        assert_eq!(4, add_two(2));
    }
}
```

三个新东西：

- `#[cfg(test)]` 写在 `mod tests` 上，表示这个模块只在 `cargo test` 时才编译，不进正式产物。
- `use super::*;`：`super` 指父模块，也就是这个文件本身。`*` 表示把它里面的东西全引进来，这样测试里才能直接写 `add_two`。`use` 是 Day21 讲过的东西。
- `assert_eq!(4, add_two(2))` 是断言：左右两边相等就过，不等就 panic。

运行：

```powershell
cargo test
```

输出大概是这样：

```
running 1 test
test tests::it_adds_two ... ok

test result: ok. 1 passed; 0 failed
```

---

## 三种断言宏

测试靠断言（assertion）判断结果对不对。三个常用宏：

### `assert!`：条件为真才通过

🧩 本块接上面第 1 块（加在同一个 `mod tests` 里面）：

```rust
#[test]
fn larger_can_hold_smaller() {
    let larger = 8;
    let smaller = 5;
    assert!(larger > smaller); // 条件为 true 才通过
}
```

### `assert_eq!` / `assert_ne!`：相等 / 不等才通过

🧩 本块接上面第 1 块（`add_two` 已经在文件里了）：

```rust
#[test]
fn it_adds_two() {
    assert_eq!(4, add_two(2));  // 左右相等才通过
    // assert_ne!(5, add_two(2)); // 左右不等才通过
}
```

失败时 `assert_eq!` 会把左右两边的实际值都打印出来，定位很方便。它要求被比较的类型实现了 `PartialEq` 和 `Debug`（结构体通常加 `#[derive(PartialEq, Debug)]` 就行，Day27 讲过 `derive`）。

### 自定义失败信息

三个宏都能再追加一条格式化消息，失败时打出来帮你定位：

🧩 本块接上面第 1 块（这段自带数据，直接加进 `mod tests`）：

```rust
#[test]
fn greeting_contains_name() {
    let result = format!("你好，{}", "Carol");
    assert!(
        result.contains("Carol"),
        "问候语里没包含名字，实际是: `{}`",
        result
    );
}
```

---

## 测试「应该会 panic」的情况

有些函数设计上就是在非法输入时 panic（`panic!` 是 Day25 学的）。比如下面这个 `Guess`，值不在 1~100 之间就直接崩。怎么测？加 `#[should_panic]`：

🧩 本块接上面第 4 块（这一段另起一个 `src/lib.rs` 的完整内容；别把它和前面那些 `mod tests` 塞进同一个文件，一个文件里不能有两个同名模块）：

```rust
pub struct Guess {
    value: i32,
}

impl Guess {
    pub fn new(value: i32) -> Guess {
        if value < 1 || value > 100 {
            panic!("Guess 值必须在 1~100 之间，得到 {}", value);
        }
        Guess { value }
    }
}

#[cfg(test)]
mod tests {
    use super::*;

    #[test]
    #[should_panic] // 只有这个测试发生 panic 才算通过
    fn greater_than_100() {
        Guess::new(200);
    }
}
```

`#[should_panic]` 把判断反过来：这个测试里 panic 了才算通过，安安稳稳跑完反而算失败。

还能用 `expected = "..."` 指定 panic 信息必须包含某段子串，测得更精确：

🧩 本块接上面第 5 块（把上面那个测试换成这个写法，属性是贴在函数上的，不能单独拎出来用）：

```rust
#[test]
#[should_panic(expected = "必须在 1~100 之间")]
fn greater_than_100() {
    Guess::new(200);
}
```

---

## 测试也能返回 Result

测试函数还能返回 `Result<(), E>`，好处是函数体里可以直接用 `?`：

🧩 本块接上面第 1 块（加进 `mod tests`）：

```rust
#[test]
fn it_works() -> Result<(), String> {
    if 2 + 2 == 4 {
        Ok(())
    } else {
        Err(String::from("2+2 不等于 4"))
    }
}
```

`?` 是 Day25 学的运算符：成功就把里面的值取出来，失败就直接返回错误。这里返回 `Err` 就表示测试失败。

> 用了 `Result` 返回值的测试不能再用 `#[should_panic]`。要检查某个调用报错，用 `assert!(value.is_err())`。

---

## 测试放在哪：单元测试 vs 集成测试

### 单元测试

和被测代码**放同一个文件**，惯例在底部写 `#[cfg(test)] mod tests`：

🧩 本块接上面第 1 块（替换 `mod tests` 里那个测试，改测同一个 `add_two`）：

```rust
// #[cfg(test)] 表示这个模块只在 cargo test 时编译，不进正式产物
#[cfg(test)]
mod tests {
    use super::*; // 连私有函数都能测

    #[test]
    fn internal() {
        assert_eq!(4, add_two(2));
    }
}
```

两个关键点：`#[cfg(test)]` 让测试代码不进发布版；`tests` 是同一个 crate 内的子模块，所以它连**私有函数都能测**。

### 集成测试

放在项目根目录的 `tests/` 文件夹（和 `src/` 同级），是**外部测试**，只能调用库的公有 API，模拟真实用户怎么用你的库：

```
my_calc/
├── src/
│   └── lib.rs
└── tests/
    └── integration_test.rs
```

`tests/integration_test.rs`：

🧩 本块接上面第 8 块（这是 `tests/` 下的独立文件，得先有 `my_calc` 这个库，靠 `cargo test` 编译）：

```rust
use my_calc; // 每个集成测试文件都要单独 use 库名

#[test]
fn it_adds_two() {
    assert_eq!(4, my_calc::add_two(2));
}
```

集成测试单独建 crate，所以要 `use my_calc;` 把库引进来，调用时写全 `my_calc::add_two`。

---

## 常用 cargo test 命令

| 命令 | 作用 |
|---|---|
| `cargo test` | 跑全部测试（默认多线程并行） |
| `cargo test 名字` | 只跑名字匹配的测试 |
| `cargo test -- --test-threads=1` | 串行跑（避免测试间干扰） |
| `cargo test -- --show-output` | 显示通过测试里的 `println!` 输出 |
| `cargo test --test integration_test` | 只跑 `tests/` 下指定文件 |

---

## 今日小结

- Rust 内置测试框架：`#[test]` 标注函数，`cargo test` 一键运行；panic 即失败
- 断言宏：`assert!(条件)`、`assert_eq!(a, b)`、`assert_ne!(a, b)`，都能加自定义消息
- `#[should_panic]` 测「该崩溃的函数」；测试也能返回 `Result` 用 `?`
- 单元测试在文件底部 `#[cfg(test)] mod tests`，可测私有函数；集成测试在 `tests/` 目录，只测公有 API

---

## 动手任务

1. 在 `src/lib.rs` 写一个 `pub fn gcd(a: i32, b: i32) -> i32`（求最大公约数），为它写三个单元测试。
2. 故意让一个断言写错（比如 `assert_eq!(5, add_two(2))`），跑 `cargo test` 看失败输出长什么样，再把断言改回来。
3. 给 `Guess::new` 写一个 `#[should_panic]` 测试，验证传 0 会 panic。

> 提示：第 1 题的欧几里得算法可以先写成 `while b != 0 { let t = b; b = a % b; a = t; }`，循环结束后返回 `a.abs()`。三个测试建议分别测「互质的两数」「有一方是 0」「两数相等」这三种情况。

---

**配合阅读**：[官方书·第11章 如何编写测试](https://rustwiki.org/zh-CN/book/ch11-01-writing-tests.html)
