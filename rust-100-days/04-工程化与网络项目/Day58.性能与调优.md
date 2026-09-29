# Day58 性能与调优

> 目标：学会用 clippy 抓代码坏味道、用 criterion 做基准测试、理解 release profile，并掌握几个最常见的 Rust 性能优化手法。

## 性能优化的正确姿势

新手常犯的错：上来就盯着某一行代码琢磨“这里能不能快一点”。专业做法是：

1. **先让代码正确**（测试全绿）
2. **再用基准测试量化**（到底慢在哪？）
3. **针对性优化**（只改测出来慢的地方）
4. **再测一遍**（确认真的快了，没改坏）

不要凭感觉优化。今天就讲这套流程里的工具和常见手法。

## clippy：Rust 的代码质量检查员

clippy 是一组额外的 lint（代码检查规则），编译器默认没开，但能抓出很多常见错误和坏味道。

### 安装与使用

```powershell
rustup component add clippy    # 装一次
cargo clippy                   # 跑一遍
```

它会像编译器一样给你警告和建议。比如你写了：

```rust
let x = 3.1415;   // 手写圆周率
```

clippy 会提示你：

```
warning: approximate value of `f64::consts::PI` found
 --> src/main.rs:2:13
  |
2 |     let x = 3.1415;
  |             ^^^^^^
  |
help: consider using the standard constant
  |
2 |     let x = std::f64::consts::PI;
```

### 常用级别

```powershell
cargo clippy -- -W clippy::pedantic     # 更严格的一组检查
cargo clippy -- -D warnings             # 把任何警告当错误（CI 里用）
```

| 级别 | 含义 |
|---|---|
| `allow` | 静默 |
| `warn` | 警告（默认） |
| `deny` | 当错误，编译失败 |

> 建议：日常写代码时常跑 `cargo clippy`，把它当第二双眼睛。

## rustfmt：自动格式化

```powershell
rustup component add rustfmt
cargo fmt                # 格式化整个项目
cargo fmt -- --check     # 只检查不改动（CI 里用）
```

不要在“该用几个空格”上和别人争论，让 rustfmt 决定。

## release profile：先确保你在测真正的性能

Day46 讲过：`cargo build` 默认不优化（`opt-level = 0`），跑出来的性能毫无意义。要测性能必须用 release：

```powershell
cargo run --release
cargo test --release
```

在 `Cargo.toml` 里可以进一步优化：

```toml
[profile.release]
opt-level = 3
lto = true              # 链接时优化
codegen-units = 1       # 配合 lto
panic = "abort"         # 不需要栈展开时可减小体积
```

## criterion：基准测试（benchmark）

标准库有个 `#[bench]` 但只能用 nightly。稳定版做基准测试的事实标准是 **criterion**。

### 添加依赖

criterion 是开发依赖，放 `[dev-dependencies]`：

```toml
[dev-dependencies]
criterion = "0.5"
```

### 写基准测试

在 `benches/` 目录下建一个文件（和 `src/`、`tests/` 平级）：

```rust
// benches/word_count.rs
use criterion::{black_box, criterion_group, criterion_main, Criterion};
use my_lib::count_words;   // 你要测的函数

fn criterion_benchmark(c: &mut Criterion) {
    // black_box 告诉编译器：这个参数值别优化掉
    let text = "the quick brown fox ".repeat(1000);
    c.bench_function("count_words 1000词", |b| {
        b.iter(|| count_words(black_box(&text)))
    });
}

criterion_group!(benches, criterion_benchmark);
criterion_main!(benches);
```

运行：

```powershell
cargo bench
```

criterion 会跑几十上百次，统计平均耗时、方差、和上次结果的对比，还会生成 HTML 报告（在 `target/criterion/`）。改完代码再跑一次，它会自动告诉你“快了 12%”或“慢了 3%”。

## 常见优化手法

下面是 Rust 里最常见效的几招，按优先级排：

### 1. 预分配 Vec：Vec::with_capacity

如果你已经知道 Vec 大概要装多少个元素，预先分配容量，避免中途反复扩容拷贝：

```rust
// 慢：push 过程中可能扩容好几次
let mut v = Vec::new();
for i in 0..10000 {
    v.push(i);
}

// 快：一次分配够
let mut v = Vec::with_capacity(10000);
for i in 0..10000 {
    v.push(i);
}
```

### 2. 减少不必要的 clone

`clone()` 会深拷贝堆数据，很贵。Day53 里 `Config::new` 用 `args[1].clone()` 是因为要拥有 String；但如果只是读，用 `&str` 就别 `to_string()`：

```rust
// 不好：参数本来就是 &str，没必要转成 String
fn process(s: &str) -> String {
    let owned = s.to_string();   // 多余的堆分配
    owned
}

// 好：能借用就借用
fn process(s: &str) -> &str {
    s
}
```

### 3. &str vs String

| 类型 | 语义 | 什么时候用 |
|---|---|---|
| `&str` | 借用的字符串切片 | 函数参数（只读） |
| `String` | 拥有所有权的字符串 | 需要持有、修改、转传时 |

写函数参数时，**优先接受 `&str` 而不是 `&String`**——更通用，因为 `&String` 能自动转成 `&str`，反过来不行。

### 4. 用迭代器而不是索引循环

Rust 编译器对迭代器的优化往往比手写 `for i in 0..len` 更好（自动向量化、消除边界检查）。前面 Day49 已经见过。

### 5. 注意整数除法和 copy

小技巧：能 `Copy` 的类型（数字等）尽量别 `clone`；遍历大集合时考虑 `iter()` 还是 `into_iter()`——前者借引用，后者拿所有权，省得后面再转。

## 今日小结

- 优化流程：正确 → 量化（criterion）→ 改 → 再测，别凭感觉
- `cargo clippy` 抓坏味道；`cargo fmt` 自动格式化
- 性能测试必须 `--release`，`[profile.release]` 可加 `lto = true`
- criterion 放 `[dev-dependencies]`，基准测试写在 `benches/` 目录，`cargo bench` 跑
- 常见优化：`Vec::with_capacity` 预分配、减少 `clone`、参数用 `&str`、优先迭代器

## 动手任务

1. 给你的项目跑一次 `cargo clippy`，把它报的警告逐个看一下，能改的改掉。
2. 在 `Cargo.toml` 的 `[profile.release]` 加上 `lto = true`，对比开不开 lto 时 release 产物的大小。
3. 给 minigrep 的 `search` 函数写一个 criterion 基准测试，用一篇长文本（repeat 几千次）测它的耗时。

> 提示：`black_box` 来自 criterion，作用是防止编译器把你要测的代码整体优化掉。

**配合阅读**：[官方书·附录D 实用开发工具](https://rustwiki.org/zh-CN/book/appendix-04-useful-development-tools.html) · [criterion.rs 官方书](https://bheisler.github.io/criterion.rs/book/) · [clippy lint 列表](https://rust-lang.github.io/rust-clippy/stable/)
