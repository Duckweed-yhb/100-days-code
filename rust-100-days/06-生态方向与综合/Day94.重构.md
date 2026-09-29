# Day94 重构

> 目标：学会识别 Rust 代码的坏味道，用「小步重构 + 测试兜底 + 编译器驱动」的方式安全改善既有代码。

## 什么是重构

重构（refactoring）= **在不改变外部行为的前提下，改善代码内部结构**。不是加功能，也不是修 bug，而是让代码更好读、更好改。

关键前提：**有测试兜底**。没测试就重构等于盲改——你怎么知道改完行为没变？

## Rust 里常见的坏味道

| 坏味道 | 表现 | 怎么改 |
|---|---|---|
| 长函数 | 一个函数 200 行干五件事 | 按职责拆成小函数 |
| 重复代码 | 三段几乎一样的逻辑 | 抽成函数或泛型 |
| 过宽抽象 | 一个 trait 塞了无关方法 | 按接口隔离，拆 trait |
| 不必要的 clone | 到处 `.clone()` 图省事 | 用借用、减少所有权转移 |
| `unwrap()` 满天飞 | 库代码里直接 panic | 改成 `?` 传错误 |

### 不必要的 clone 是 Rust 特有重灾区

新手为了让借用检查器通过，到处 `.clone()`。每次 clone 都有拷贝开销。重构方向：**尽量传引用 `&T`，缩小所有权范围**。

## 重构心法：编译器是你的安全网

Rust 重构特别舒服，因为编译器 + 测试会帮你兜底：

1. 先写/跑测试，确认现在行为正确
2. 小步改，每改一点就 `cargo check` 看编译过没过
3. 编译过了就跑测试，确认行为没变
4. 重复这个循环

**大胆改，编译器会拉住你**。这是 Rust 比动态语言安全得多的地方。

## 对照小例：before → after

**改之前**——一个又长又重复的函数：

```rust
fn report(users: &[User]) -> String {
    let mut out = String::new();
    // 统计男生
    let mut male = 0;
    let mut male_age = 0;
    for u in users {
        if u.gender == "male" {
            male += 1;
            male_age += u.age;
        }
    }
    if male > 0 {
        out.push_str(&format!("男性 {} 人，平均年龄 {:.1}\n", male, male_age as f64 / male as f64));
    }
    // 统计女生（几乎一样的代码又写一遍）
    let mut female = 0;
    let mut female_age = 0;
    for u in users {
        if u.gender == "female" {
            female += 1;
            female_age += u.age;
        }
    }
    if female > 0 {
        out.push_str(&format!("女性 {} 人，平均年龄 {:.1}\n", female, female_age as f64 / female as f64));
    }
    out
}
```

**改之后**——抽出通用函数：

```rust
fn avg_age_of(users: &[User], gender: &str) -> Option<(usize, f64)> {
    let group: Vec<&User> = users.iter().filter(|u| u.gender == gender).collect();
    if group.is_empty() {
        return None;
    }
    let n = group.len();
    let sum: u32 = group.iter().map(|u| u.age).sum();
    Some((n, sum as f64 / n as f64))
}

fn report(users: &[User]) -> String {
    let mut out = String::new();
    for g in ["male", "female"] {
        if let Some((n, avg)) = avg_age_of(users, g) {
            out.push_str(&format!("{} {} 人，平均年龄 {:.1}\n", g, n, avg));
        }
    }
    out
}
```

行为没变，但重复逻辑抽干净了，以后加第三个性别只改一行。

## 什么时候不该重构

- 马上要上线、没测试、没时间 → 别碰
- 「我觉得这样更好看」但没具体问题 → 克制
- 先让测试变红（有 bug），再让测试变绿（修好），最后才重构

## 今日小结

- 重构 = 不改外部行为，只改善内部结构
- 前提是有测试兜底，小步走，每步跑测试
- Rust 特有坏味道：到处 clone、库代码里 unwrap
- 编译器是安全网，大胆拆函数、抽重复
- 时机不对别硬重构

## 动手任务

1. 找你之前写的一个长函数，按职责拆成 2~3 个小函数
2. 在那段代码里找出一个不必要的 `.clone()`，改成借用
3. 重构前后都跑一遍测试，确认行为没变

**配合阅读**：[Rust by Example - 常见概念](https://doc.rust-lang.org/rust-by-example/)
