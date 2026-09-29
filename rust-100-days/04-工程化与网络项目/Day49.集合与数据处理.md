# Day49 集合与数据处理

> 目标：把 `Vec`、`HashMap`、`HashSet` 和迭代器串起来，用一组实战小例子（词频统计、分组、筛选）学会 Rust 里怎么处理数据。

## 三个内置集合回顾

你在前面已经见过这三个集合，今天把它们放到真实数据处理场景里用：

| 集合 | 什么时候用 | 类比 |
|---|---|---|
| `Vec<T>` | 有序、可重复、按索引访问的列表 | 数组 |
| `HashMap<K, V>` | 键值对，按键快速查找 | 字典/Map |
| `HashSet<T>` | 不重复的集合，快速判断“有没有” | 数学里的集合 |

数据处理的套路基本是：**读数据 → 用迭代器转换/筛选 → 收进某个集合 → 再汇总统计**。

## 例子一：词频统计

这是最经典的集合 + 迭代器练习：给一段英文文本，统计每个单词出现了几次。

```rust
use std::collections::HashMap;

fn main() {
    let text = "the quick brown fox jumps over the lazy dog the dog barks";

    let mut counts: HashMap<&str, u32> = HashMap::new();

    // split_whitespace() 按空白切词，得到迭代器
    for word in text.split_whitespace() {
        // entry API：键存在就拿到值的可变引用，不存在就插入 0
        *counts.entry(word).or_insert(0) += 1;
    }

    // 按出现次数从高到低打印
    let mut sorted: Vec<_> = counts.iter().collect();
    sorted.sort_by(|a, b| b.1.cmp(a.1));  // b.1 降序

    for (word, count) in sorted {
        println!("{}: {}", word, count);
    }
}
```

关键点：

- `entry(word).or_insert(0)` 是 HashMap 最常用的 idiom：不用先 `contains_key` 再 `insert`，一步搞定
- `*counts[...] += 1` 前面的 `*` 是解引用，因为 `or_insert` 返回的是 `&mut u32`
- `HashMap` 本身无序，想排序就 `collect` 成 `Vec` 再排

运行下面命令看看输出（把上面代码贴到 `src/main.rs`）：

```powershell
cargo run
```

## 例子二：分组

把一批学生按成绩分档（及格/不及格）：

```rust
use std::collections::HashMap;

#[derive(Debug)]
struct Student {
    name: String,
    score: u32,
}

fn main() {
    let students = vec![
        Student { name: "Alice".into(), score: 85 },
        Student { name: "Bob".into(), score: 55 },
        Student { name: "Carol".into(), score: 92 },
        Student { name: "Dave".into(), score: 48 },
    ];

    // key 是分档标签，value 是该档学生名单
    let mut groups: HashMap<&str, Vec<&str>> = HashMap::new();

    for s in &students {
        let label = if s.score >= 60 { "及格" } else { "不及格" };
        groups.entry(label).or_insert_with(Vec::new).push(&s.name);
    }

    for (label, names) in &groups {
        println!("{}: {:?}", label, names);
    }
}
```

`or_insert_with(Vec::new)` 和 `or_insert(Vec::new())` 的区别：前者是惰性的——只有真要插入时才调用 `Vec::new()`；后者无论键是否存在都会先构造一个空 Vec（虽然空 Vec 没分配堆内存，但养成习惯用 `or_insert_with` 更好）。

## 例子三：用 HashSet 去重和求交集

```rust
use std::collections::HashSet;

fn main() {
    let a: HashSet<i32> = vec![1, 2, 3, 4].into_iter().collect();
    let b: HashSet<i32> = vec![3, 4, 5, 6].into_iter().collect();

    // 交集：同时在 a 和 b 里
    println!("交集: {:?}", a.intersection(&b).collect::<Vec<_>>());
    // 并集：在 a 或 b 里
    println!("并集: {:?}", a.union(&b).collect::<Vec<_>>());
    // 差集：在 a 但不在 b
    println!("差集: {:?}", a.difference(&b).collect::<Vec<_>>());
    // 判断是否包含
    println!("a 包含 3 吗: {}", a.contains(&3));
}
```

`HashSet` 很适合做“去重”：把一个 Vec 转成 HashSet 再转回来，重复元素就没了：

```rust
let nums = vec![1, 2, 2, 3, 3, 3];
let unique: HashSet<i32> = nums.into_iter().collect();
println!("{:?}", unique);  // {1, 2, 3}（顺序不定）
```

## 迭代器链式处理：再看一个完整例子

把迭代器的 `map` / `filter` / `collect` / `sum` / `enumerate` 串起来——这是 Rust 数据处理的日常写法：

```rust
fn main() {
    let nums = [12, 45, 6, 78, 90, 3, 27];

    // 目标：挑出偶数，每个乘 10，求和
    let total: i32 = nums
        .iter()
        .filter(|&&x| x % 2 == 0)   // 保留偶数
        .map(|&x| x * 10)           // 每个乘 10
        .sum();                     // 求和

    println!("偶数乘10后的和: {}", total);  // (12+6+78+90)*10 = 1860

    // enumerate 给每个元素配索引
    for (i, &x) in nums.iter().enumerate() {
        println!("第 {} 个是 {}", i, x);
    }
}
```

> 记住：`iter()` 遍历的是引用 `&i32`，所以闭包参数写 `|&x|` 或 `|&&x|` 来模式匹配解引用。编译器的类型提示会帮你。

## 字符串处理的小套路

处理文本时经常要 `split`、`trim`、`to_lowercase`：

```rust
fn main() {
    let line = "  Hello, Rust!  ";
    let trimmed = line.trim();                    // 去掉首尾空白
    let lower = trimmed.to_lowercase();           // 转小写（注意返回 String）
    for word in lower.split(|c: char| !c.is_alphabetic()) {
        // 按“非字母字符”切，标点也被当分隔符
        if !word.is_empty() {
            println!("词: {}", word);
        }
    }
}
```

## 今日小结

- 词频统计核心套路：`HashMap<&str, u32>` + `entry(word).or_insert(0)` 累加
- 分组核心套路：`HashMap<&str, Vec<&T>>` + `or_insert_with(Vec::new)` 后 `push`
- `HashSet` 做去重、交集、并集、差集；`contains` O(1) 判断存在
- 迭代器链：`iter().filter().map().collect()/.sum()`，零成本抽象，和手写循环一样快
- 文本处理常用 `split_whitespace`、`trim`、`to_lowercase`

## 动手任务

1. 读一个英文文本文件（自己准备一个 `.txt`），统计其中词频最高的 5 个单词。
2. 给一个 `Vec<i32>`，用迭代器链求出其中所有奇数的平方和。
3. 用 `HashSet` 实现：给定两个单词列表，找出它们共同出现过的单词（交集）。

> 提示：读文件用 Day48 学的 `std::fs::read_to_string`；词频统计记得先 `to_lowercase()` 统一大小写，否则 `The` 和 `the` 会被算成两个词。

**配合阅读**：[官方书·第8章 常见集合](https://rustwiki.org/zh-CN/book/ch08-00-common-collections.html) · [官方书·13.2 使用迭代器处理元素序列](https://rustwiki.org/zh-CN/book/ch13-02-iterators.html)
