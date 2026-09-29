# Day29 自动化测试

> 目标：学会用 Rust 内置的测试框架写单元测试，用 `cargo test` 一键验证代码对不对。

## Rust 自带测试框架

别的语言往往要装一堆测试库。Rust 把测试能力直接做进了语言和 Cargo：你只要给一个函数加 `#[test]` 属性，跑 `cargo test`，它就自动找到并执行。

核心观念特别简单：**测试就是会 panic 的函数——panic 了就算失败，不 panic 就算通过。**

## 第一个测试

新建一个库包最方便：

```powershell
cargo new --lib my_calc
cd my_calc
```

把 `src/lib.rs` 改成：

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

运行：

```powershell
cargo test
```

你会看到类似输出：

```
running 1 test
test tests::it_adds_two ... ok

test result: ok. 1 passed; 0 failed
```

## 三种断言宏

测试靠断言（assertion）判断结果对不对。三个常用宏：

### `assert!` —— 条件为真才通过

```rust
#[test]
fn larger_can_hold_smaller() {
    let larger = 8;
    let smaller = 5;
    assert!(larger > smaller); // 条件为 true 才通过
}
```

### `assert_eq!` / `assert_ne!` —— 相等 / 不等才通过

```rust
#[test]
fn it_adds_two() {
    assert_eq!(4, add_two(2));  // 左右相等才通过
    // assert_ne!(5, add_two(2)); // 左右不等才通过
}
```

失败时 `assert_eq!` 会自动打印出左右两边的实际值，非常方便。它要求被比较的类型实现了 `PartialEq` 和 `Debug`（结构体通常加 `#[derive(PartialEq, Debug)]` 即可）。

### 自定义失败信息

三个宏都能追加一条格式化消息，失败时打出来帮你定位：

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

## 测试「应该会 panic」的情况

有些函数设计就是在非法输入时 panic（比如 Day25 的 `Guess::new(200)`）。怎么测？加 `#[should_panic]`：

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

还能加 `expected = "..."` 指定 panic 信息必须包含某段子串，测更精确。

## 测试也能返回 Result

测试函数还能返回 `Result<(), E>`，好处是函数体里可以直接用 `?`：

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

> 用了 `Result` 返回值的测试就不能再用 `#[should_panic]` 了；要检查错误用 `assert!(value.is_err())`。

## 测试放在哪：单元测试 vs 集成测试

### 单元测试

和被测代码**放同一个文件**，惯例在底部写 `#[cfg(test)] mod tests`：

```rust
// #[cfg(test)] 表示这个模块只在 cargo test 时编译，不进正式产物
#[cfg(test)]
mod tests {
    use super::*; // 连私有函数都能测！

    #[test]
    fn internal() {
        assert_eq!(4, add_two(2));
    }
}
```

两个关键：`#[cfg(test)]` 让测试代码不进发布版；因为 `tests` 是同 crate 内的子模块，它连**私有函数都能测**。

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

```rust
use my_calc; // 每个集成测试文件都要单独 use 库名

#[test]
fn it_adds_two() {
    assert_eq!(4, my_calc::add_two(2));
}
```

## 常用 cargo test 命令

| 命令 | 作用 |
|---|---|
| `cargo test` | 跑全部测试（默认多线程并行） |
| `cargo test 名字` | 只跑名字匹配的测试 |
| `cargo test -- --test-threads=1` | 串行跑（避免测试间干扰） |
| `cargo test -- --show-output` | 显示通过测试里的 `println!` 输出 |
| `cargo test --test integration_test` | 只跑 `tests/` 下指定文件 |

## 今日小结

- Rust 内置测试框架：`#[test]` 标注函数，`cargo test` 一键运行；panic 即失败
- 断言宏：`assert!(条件)`、`assert_eq!(a,b)`、`assert_ne!(a,b)`，都能加自定义消息
- `#[should_panic]` 测「该崩溃的函数」；测试也能返回 `Result` 用 `?`
- 单元测试在文件底部 `#[cfg(test)] mod tests`，可测私有函数；集成测试在 `tests/` 目录，只测公有 API

## 动手任务

1. 在 `src/lib.rs` 写一个 `pub fn gcd(a: i32, b: i32) -> i32`（求最大公约数），为它写三个单元测试。
2. 故意让一个断言写错（比如 `assert_eq!(5, add_two(2))`），跑 `cargo test` 看失败输出长什么样。
3. 给 `Guess::new` 写一个 `#[should_panic]` 测试，验证传 0 会 panic。

> 提示：欧几里得算法——`while b != 0 { let t = b; b = a % b; a = t; }`，返回 `a.abs()`。

**配合阅读**：[官方书·第11章 如何编写测试](https://rustwiki.org/zh-CN/book/ch11-01-writing-tests.html)
