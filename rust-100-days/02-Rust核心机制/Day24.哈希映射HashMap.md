# Day24 哈希映射 HashMap

> **前置知识**：Day18 Option（`get` 的返回值）、Day19 match（把 `get` 的结果取出来）、Day22 向量 Vec（用下标取值和用键取值的区别）
> **难度**：入门
> **预计阅读时间**：20 分钟
> **代码说明**：本章代码都放在**同一个** `main.rs` 里。每块都有完整的 `use` 和 `fn main()`，单独复制就能跑。
> 每块前面都标了 `📦`（能单独跑）。两块的 `main` 不能同时塞进一个文件。

---

## HashMap 是什么

`Vec` 靠数字下标取值，适合回答「第几个」。要是想按名字找东西，比如队伍名查得分、单词查出现次数、学号查姓名，就得用**哈希映射 `HashMap<K, V>`**。它靠**键（key）**查**值（value）**。

`<K, V>` 又是尖括号占位符（Day22 见过一次）：`K` 是键的类型，`V` 是值的类型。同一个 HashMap 里所有键必须同类型，所有值也必须同类型。

用之前先 `use std::collections::HashMap;`。HashMap 不在 prelude 里，prelude 是 Rust 自动带进每个文件的一批常用名字，`Vec`、`String` 都在里面，HashMap 不在，所以得自己引。

## 创建与插入

📦 本块能单独跑：

```rust
use std::collections::HashMap;

fn main() {
    let mut scores = HashMap::new();

    scores.insert(String::from("Blue"), 10);
    scores.insert(String::from("Yellow"), 50);

    println!("{:?}", scores); // 调试格式 {:?} 能整张表打印出来
}
```

`HashMap::new()` 造出来是空的，键值类型只能从后面的 `insert` 推出来，这里推成 `HashMap<String, i32>`。

**所有权提醒**：往表里 `insert` 一个有所有权的值（`String` 就是），这个值会被移动进去，原来的变量之后就用不了了。`i32` 这种 `Copy` 类型是直接拷一份进去，原变量照常能用。这是 Day10 讲的移动和拷贝。

## 用键查值

`get` 收键的引用，返回 `Option<&V>`：键在就给 `Some`，不在就给 `None`。

📦 本块能单独跑：

```rust
use std::collections::HashMap;

fn main() {
    let mut scores = HashMap::new();
    scores.insert(String::from("Blue"), 10);
    scores.insert(String::from("Yellow"), 50);

    let team_name = String::from("Blue");
    let score = scores.get(&team_name); // 类型是 Option<&i32>

    match score {
        Some(&v) => println!("蓝队得分: {}", v), // 走这里
        None => println!("蓝队没有记录"),
    }

    // 查一个不存在的键
    match scores.get("Green") {
        Some(&v) => println!("绿队得分: {}", v),
        None => println!("绿队没有记录"), // 走这里
    }
}
```

`Some(&v)` 里的 `&` 是模式的一部分，作用是把 `Option<&i32>` 里那层引用拆掉，拆完 `v` 就是 `i32` 本身。写成 `Some(v)` 也行，那样 `v` 是 `&i32`，照样打印得出来。

注意 `scores.get(&team_name)` 传的是 `&String`，而 `scores.get("Green")` 传的是 `&str`，两者都能查同一个 `String` 键，`get` 允许用 `&str` 来查 `String`。

## 遍历

用 `for` 循环遍历所有键值对。HashMap **不保证顺序**，哈希表本身就是无序的，每次跑出来的排列可能都不一样。

📦 本块能单独跑：

```rust
use std::collections::HashMap;

fn main() {
    let mut scores = HashMap::new();
    scores.insert(String::from("Blue"), 10);
    scores.insert(String::from("Yellow"), 50);

    for (key, value) in &scores {
        println!("{}: {}", key, value);
    }
    // 可能是 Yellow: 50 然后 Blue: 10，也可能反过来
}
```

`&scores` 遍历出来的每一项是一对 `(键, 值)`，用 `(key, value)` 一次接住两个，这是 Day19 讲的元组模式。

## 更新的三种情况

### 1. 同键再 insert，直接覆盖

📦 本块能单独跑：

```rust
use std::collections::HashMap;

fn main() {
    let mut scores = HashMap::new();
    scores.insert(String::from("Blue"), 10);
    scores.insert(String::from("Blue"), 25); // 同一个键，旧的 10 被顶掉

    println!("{:?}", scores); // {"Blue": 25}
}
```

### 2. entry 配 or_insert，不存在才插入

`entry(键)` 返回一个 `Entry` 枚举，代表「这个键在表里的那个位置」。接着调 `or_insert(值)`：键不存在就插入这个值，已经存在就什么也不做。

📦 本块能单独跑：

```rust
use std::collections::HashMap;

fn main() {
    let mut scores = HashMap::new();
    scores.insert(String::from("Blue"), 10);

    // Yellow 不在表里 → 插入 50
    scores.entry(String::from("Yellow")).or_insert(50);
    // Blue 已经在表里 → 保持 10
    scores.entry(String::from("Blue")).or_insert(50);

    println!("{:?}", scores); // {"Blue": 10, "Yellow": 50}
}
```

### 3. 拿旧值来更新

`or_insert` 的返回值是那个键对应值的**可变引用 `&mut V`**，可以直接在它上面改。经典的用法是词频统计：

📦 本块能单独跑：

```rust
use std::collections::HashMap;

fn main() {
    let text = "hello world wonderful world";
    let mut map = HashMap::new();

    for word in text.split_whitespace() {
        // 第一次见到这个单词就放进 0，拿到的是它的可变引用
        let count = map.entry(word).or_insert(0);
        *count += 1; // * 解引用，把引用指向的那个数加 1
    }

    println!("{:?}", map); // {"world": 2, "hello": 1, "wonderful": 1}
}
```

三行就完成了统计：`split_whitespace()` 把句子按空白切成一个个词，每遇到一个词，表里没有就先放 0，然后加 1。

`*count += 1` 里的 `*` 是解引用，跟 Day22 的 `*num *= 10` 一个道理：`count` 本身是 `&mut i32`，要改它指向的值必须加 `*`。

这里的键类型推断成了 `&str`，因为 `word` 就是 `&str`。`text` 是字符串字面量，活到程序结束，所以借来的这些词一直有效。

## 常用 API 速查

| 操作 | 写法 | 说明 |
|---|---|---|
| 创建 | `HashMap::new()` | 先 `use` |
| 插入 / 覆盖 | `map.insert(k, v)` | 同键重复会覆盖 |
| 查询 | `map.get(&k)` | 返回 `Option<&V>` |
| 遍历 | `for (k, v) in &map` | 顺序不保证 |
| 删除 | `map.remove(&k)` | |
| 键存在？ | `map.contains_key(&k)` | 返回 bool |
| 不存在才插 | `map.entry(k).or_insert(v)` | 返回 `&mut V` |
| 长度 | `map.len()` | 键值对个数 |

## 今日小结

- `HashMap<K, V>` 用键查值，用之前要 `use std::collections::HashMap`
- `insert` 插入，同键会覆盖；`get` 查询返回 `Option<&V>`
- 遍历写 `for (k, v) in &map`，顺序不保证
- `entry(键).or_insert(默认值)` 表示「不存在才插入」，返回那个值的可变引用
- 配 `*count += 1` 就能统计词频

## 动手任务

1. 建一个 `HashMap<String, i32>`，塞三个同学的成绩，查其中一个人的分数并打印。
2. 用 `entry().or_insert(0)` 统计 `"a dog ran in a dog park"` 里每个单词出现几次。
3. 对已经存在的键再 `insert` 一个新值，确认旧值被覆盖。

> 第 2 题提示：`split_whitespace()` 返回迭代器，直接 `for word in ...` 就行；`word` 是 `&str`，可以直接当键。
> 第 3 题提示：两次 insert 之后分别 `println!("{:?}", map)`，对比着看输出。

**配合阅读**：[官方书·第8章 哈希 map](https://rustwiki.org/zh-CN/book/ch08-03-hash-maps.html)
