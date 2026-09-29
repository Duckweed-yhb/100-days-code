# Day57 测试进阶

> 目标：在 Day29 单元测试的基础上，学会写集成测试（tests/ 目录）和文档测试（doc test），让项目测试覆盖更完整。

## 三种测试回顾

Rust 项目里测试分三类，位置和目的都不同：

| 类型 | 位置 | 测什么 |
|---|---|---|
| 单元测试 | `src/*.rs` 文件底部的 `#[cfg(test)] mod tests` | 内部逻辑、私有函数 |
| 集成测试 | `tests/` 目录下的 `.rs` 文件 | 库的公有 API（像外部用户一样用） |
| 文档测试 | 文档注释 `///` 里的代码块 | 示例代码能不能跑通 |

Day29 已经学过单元测试，今天重点讲后两种。

## 集成测试：tests/ 目录

集成测试在项目根目录的 `tests/` 文件夹里（和 `src/` 平级）。它是**外部测试**——只能 `use` 库的公有接口，和最终用户用你的库的方式一模一样。

### 项目结构

```
my_lib/
├── src/
│   ├── lib.rs        # 库代码
│   └── main.rs       # （如果有二进制）
└── tests/
    └── integration_test.rs   # 集成测试
```

### 写第一个集成测试

```rust
// tests/integration_test.rs
use my_lib;   // 每个集成测试文件都要单独 use 你的库

#[test]
fn add_two_works() {
    assert_eq!(4, my_lib::add_two(2));
}

#[test]
fn hello_contains_name() {
    let result = my_lib::greeting("Alice");
    assert!(result.contains("Alice"));
}
```

运行：

```powershell
cargo test
```

你会看到输出分三段：单元测试、文档测试、集成测试。只跑集成测试里这一个文件：

```powershell
cargo test --test integration_test
```

关键点：

- `tests/` 里的文件**不需要** `#[cfg(test)]`——Cargo 本来就只在 `cargo test` 时编译它们
- 每个 `.rs` 文件会被当成**独立的 crate** 编译，所以每个文件都要 `use my_lib;`
- 集成测试碰不到私有函数，这是故意的——它验证的是对外契约

### 共享辅助模块

多个集成测试文件可能需要共用一个 setup 函数。**不要**写成 `tests/helpers.rs`——那样 Cargo 会把它当成一个独立测试 crate，运行时显示 `running 0 tests`。

正确做法是建子目录 `tests/common/mod.rs`：

```rust
// tests/common/mod.rs
pub fn setup() {
    // 比如创建临时目录、初始化日志等
    println!("setup 完成");
}
```

然后在测试文件里声明这个模块：

```rust
// tests/integration_test.rs
use my_lib;

mod common;   // 声明模块（来自 tests/common/mod.rs）

#[test]
fn it_works() {
    common::setup();
    assert_eq!(4, my_lib::add_two(2));
}
```

## 文档测试：让示例代码永不撒谎

在 Day47 学过，`///` 文档注释里的代码块会被 `cargo test` 自动编译运行。这叫**文档测试（doc test）**。

```rust
/// 给数字加一。
///
/// # 示例
///
/// ```
/// use my_lib::add_one;
/// assert_eq!(add_one(5), 6);
/// ```
pub fn add_one(x: i32) -> i32 {
    x + 1
}
```

跑 `cargo test` 时，你会看到 `Doc-tests my_lib` 这一段——上面那个 `assert_eq!` 真的被编译执行了。

为什么这很重要？因为**示例代码一旦跑不通，测试就失败**，逼着你在改函数签名时同步更新文档。文档永远不会过时。

几个常用技巧：

```rust
/// 这个函数会 panic（文档测试默认期望不 panic，所以要标注 ignore 或用 should_panic）
///
/// # 示例
///
/// ```should_panic
/// my_lib::do_dangerous_thing();
/// ```
pub fn do_dangerous_thing() { panic!("炸了") }

/// 这个示例只是演示，不想真的运行
///
/// ```no_run
/// // 这段代码只在文档里展示，不编译运行
/// println!("只是看看");
/// ```
pub fn just_demo() {}
```

| 标注（写在 ``` 后面） | 含义 |
|---|---|
| `rust`（默认） | 编译并运行，通过才算过 |
| `should_panic` | 期望代码 panic |
| `no_run` | 编译但不运行（比如启动服务器这种会阻塞的示例） |
| `ignore` | 不跑（比如很慢的示例） |

## 常用 cargo test 命令回顾

| 命令 | 作用 |
|---|---|
| `cargo test` | 跑所有测试（单元 + 集成 + 文档） |
| `cargo test 名字` | 只跑名字含该字符串的测试 |
| `cargo test --test 文件名` | 只跑某个集成测试文件 |
| `cargo test -- --nocapture` | 显示测试里的 `println!` 输出 |
| `cargo test -- --test-threads=1` | 串行跑（测试间有共享状态时用） |

## 今日小结

- 单元测试在 `src/` 文件内的 `#[cfg(test)] mod tests`，能测私有函数
- 集成测试在 `tests/` 目录，只测公有 API，每个文件独立 crate、都要 `use 库名`
- 共享辅助代码放 `tests/common/mod.rs`，不要直接放 `tests/helpers.rs`
- 文档测试：`///` 里的代码块会被 `cargo test` 自动跑，保证示例代码不过时
- `should_panic`、`no_run`、`ignore` 写在文档代码块的 ``` 后面，控制文档测试行为

## 动手任务

1. 给 minigrep 的 lib.rs 写一个 `tests/integration_test.rs`，从外部调 `minigrep::search` 测一个用例。
2. 给 `search` 函数的文档注释加一段 `# Examples`，跑 `cargo test` 确认 Doc-tests 部分跑了你的示例。
3. 故意把文档示例里的 `assert_eq!` 改成错误的值，跑 `cargo test`，观察 Doc-tests 失败——体会“文档即测试”。

> 提示：集成测试要测的函数必须是 `pub` 的；如果你的库还没有 `src/lib.rs`，先把逻辑从 main.rs 挪过去（Day53 做过）。

**配合阅读**：[官方书·11.3 测试的组织结构](https://rustwiki.org/zh-CN/book/ch11-03-test-organization.html)
