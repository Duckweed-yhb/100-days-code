# Day24 哈希映射 HashMap

> 目标：学会用 `HashMap<K, V>` 存储键值对，掌握插入、查询、遍历和「不存在才插入」的常用写法。

## HashMap 是什么

`Vec` 是用数字下标取值的列表；而 **哈希映射 `HashMap<K, V>`** 是用**键（key）**查**值（value）**的字典。现实例子：队伍名 → 得分、单词 → 出现次数、学生 ID → 姓名。

它需要先 `use std::collections::HashMap;`——HashMap 不在编译器自动引入的 prelude 里。

## 创建与插入

```rust
use std::collections::HashMap;

let mut scores = HashMap::new();

scores.insert(String::from("Blue"), 10);
scores.insert(String::from("Yellow"), 50);
```

和 Vec 一样，数据在堆上；所有键必须同类型，所有值必须同类型。

**所有权提醒**：插入 `String` 这类有所有权的值时，它会被移动进 HashMap，原变量就不能再用了；而 `i32` 这类 `Copy` 类型是直接拷一份进去。

## 用键查值

`get` 接收键的引用，返回 `Option<&V>`——键存在给 `Some`，不存在给 `None`：

```rust
use std::collections::HashMap;

let mut scores = HashMap::new();
scores.insert(String::from("Blue"), 10);
scores.insert(String::from("Yellow"), 50);

let team_name = String::from("Blue");
let score = scores.get(&team_name); // Some(&10)

match score {
    Some(&v) => println!("蓝队得分: {}", v), // 走这
    None => println!("蓝队没有记录"),
}
```

## 遍历

用 `for` 循环遍历所有键值对。注意 HashMap **不保证顺序**（哈希表本就无序）：

```rust
use std::collections::HashMap;

let mut scores = HashMap::new();
scores.insert(String::from("Blue"), 10);
scores.insert(String::from("Yellow"), 50);

for (key, value) in &scores {
    println!("{}: {}", key, value);
}
// Yellow: 50
// Blue: 10   （输出顺序可能不同）
```

## 更新：三种情况

### 1. 直接覆盖

同一个键再 `insert`，旧值被替换：

```rust
scores.insert(String::from("Blue"), 10);
scores.insert(String::from("Blue"), 25); // Blue 变成 25，旧的 10 被覆盖
```

### 2. 键不存在才插入：`entry` + `or_insert`

这是 HashMap 最常用的惯用法。`entry(键)` 返回一个 `Entry` 枚举；`or_insert(值)` 表示「不存在就插入这个值，存在就什么也不做」：

```rust
use std::collections::HashMap;

let mut scores = HashMap::new();
scores.insert(String::from("Blue"), 10);

// Yellow 不存在 → 插入 50
scores.entry(String::from("Yellow")).or_insert(50);
// Blue 已存在 → 保持不变，仍是 10
scores.entry(String::from("Blue")).or_insert(50);

println!("{:?}", scores);
```

### 3. 基于旧值更新

`or_insert` 返回那个键对应值的**可变引用 `&mut V`**，你可以在它基础上改。最经典的例子是**单词计数**：

```rust
use std::collections::HashMap;

let text = "hello world wonderful world";
let mut map = HashMap::new();

for word in text.split_whitespace() {
    // 第一次见到某单词，插入 0；之后拿到它的可变引用
    let count = map.entry(word).or_insert(0);
    *count += 1; // 解引用，计数 +1
}

println!("{:?}", map);
// {"world": 2, "hello": 1, "wonderful": 1}
```

这段三行代码是 HashMap 的灵魂，务必看懂：`split_whitespace()` 把文本切成一个个词，每遇到一个词，没有就先放 0，然后 +1。最后就得到每个词出现几次。

## 常用 API 速查

| 操作 | 写法 | 说明 |
|---|---|---|
| 创建 | `HashMap::new()` | 先 use |
| 插入/覆盖 | `map.insert(k, v)` | 同键重复会覆盖 |
| 查询 | `map.get(&k)` | 返回 `Option<&V>` |
| 遍历 | `for (k, v) in &map` | 顺序任意 |
| 删 | `map.remove(&k)` | |
| 键存在？ | `map.contains_key(&k)` | 返回 bool |
| 不存在才插 | `map.entry(k).or_insert(v)` | 返回 `&mut V` |
| 长度 | `map.len()` | |

## 今日小结

- `HashMap<K, V>` 用键查值，要 `use std::collections::HashMap`
- `insert` 插入（同键覆盖），`get` 查询返回 `Option<&V>`
- 遍历用 `for (k, v) in &map`，顺序不保证
- `entry(键).or_insert(默认值)` 是「不存在才插入」的惯用法，返回可变引用
- 配合 `*count += 1` 能优雅地实现词频统计

## 动手任务

1. 建一个 `HashMap<String, i32>`，存三个同学的成绩，查询其中一个人的分数并打印。
2. 用 `entry().or_insert(0)` 统计短语 `"a dog ran in a dog park"` 里每个单词出现几次。
3. 对已存在的键再 `insert` 一个新值，确认旧值被覆盖。

> 提示：`text.split_whitespace()` 返回的是迭代器，直接 `for word in ...` 就行；`word` 是 `&str`，可以直接当 HashMap 的键。

**配合阅读**：[官方书·第8章 哈希 map](https://rustwiki.org/zh-CN/book/ch08-03-hash-maps.html)
