# Day32 迭代器

> 目标：搞懂 `Iterator` trait，熟练用 `map` / `filter` / `collect` 这条函数式流水线处理序列，并理解为什么它和手写循环一样快。

## 迭代器是什么

迭代器（iterator）是一个「按顺序挨个产出元素」的对象。你之前写 `for x in vec` 时，Rust 就在背后用迭代器。它最大的特点是**惰性（lazy）**：你创建一个迭代器时，它什么都不干；只有你真正去「要」下一个元素时，它才算一个。

打个比方：迭代器像自动售货机的取货队列，你不按按钮（不调用 `next`），它不会自己掉东西出来。

```rust
let v1 = vec![1, 2, 3];
let v1_iter = v1.iter(); // 创建迭代器——此时啥也没发生

for val in v1_iter {     // for 循环在背后反复调 next()，这才真正遍历
    println!("got: {}", val);
}
```

## Iterator trait 的核心

所有迭代器都实现了标准库的 `Iterator` trait，它的核心就两个东西：

```rust
pub trait Iterator {
    type Item;                               // 每次产出的元素是什么类型
    fn next(&mut self) -> Option<Self::Item>; // 返回 Some(元素)，没了就返回 None
}
```

手动调 `next` 感受一下（注意迭代器要 `mut`，因为 `next` 会改变它内部的「当前位置」）：

```rust
fn main() {
    let v = vec![1, 2, 3];
    let mut iter = v.iter(); // 要 mut，next 会移动内部游标

    assert_eq!(iter.next(), Some(&1));
    assert_eq!(iter.next(), Some(&2));
    assert_eq!(iter.next(), Some(&3));
    assert_eq!(iter.next(), None); // 取完了，返回 None
}
```

## 三种创建迭代器的方法

| 方法 | 产出的元素 | 什么时候用 |
|---|---|---|
| `iter()` | `&T`（不可变引用） | 只读遍历 |
| `iter_mut()` | `&mut T`（可变引用） | 要修改每个元素 |
| `into_iter()` | `T`（拿走所有权） | 遍历完原集合不再用 |

```rust
let mut nums = vec![1, 2, 3];
for n in nums.iter_mut() {
    *n *= 10; // 透过可变引用改原 Vec
}
println!("{:?}", nums); // [10, 20, 30]
```

## 消费适配器：把流水线「跑起来」

迭代器本身惰性，必须有人「拉」它。**消费适配器（consumer）**会反复调 `next()`，把整条流水线跑完，最后产出一个具体值。

最常用的两个：

```rust
fn main() {
    let v = vec![1, 2, 3];
    let total: i32 = v.iter().sum(); // sum 把迭代器吃光，返回总和
    assert_eq!(total, 6);

    // collect 把结果收集成另一个集合，比如 Vec
    let doubled: Vec<i32> = v.iter().map(|x| x * 2).collect();
    assert_eq!(doubled, vec![2, 4, 6]);
}
```

> `collect()` 经常要你标类型，比如 `collect::<Vec<i32>>()`。因为它能收集成 Vec、HashSet 等很多种集合，编译器猜不出来你要哪个。

## 迭代适配器：把一个迭代器变成另一个

**迭代适配器（iterator adapter）**会返回一个新的迭代器，你可以像流水线一样一路 `.map().filter().zip()` 串下去——但它们仍然是惰性的，**最后必须接一个消费适配器**才会真正执行。

```rust
fn main() {
    let v: Vec<i32> = vec![1, 2, 3, 4, 5]
        .iter()
        .map(|x| x * x)        // 每个元素平方：1,4,9,16,25
        .filter(|x| x % 2 == 1) // 只留奇数：1,9,25
        .collect();
    println!("{:?}", v); // [1, 9, 25]
}
```

几个高频适配器：

| 适配器 | 作用 |
|---|---|
| `.map(|x| ...)` | 对每个元素做变换 |
| `.filter(|x| ...)` | 保留闭包返回 true 的元素 |
| `.enumerate()` | 给每个元素配上索引，产出 `(下标, &元素)` |
| `.zip(另一个迭代器)` | 两个迭代器配对成 `(a, b)` |
| `.take(n)` | 只取前 n 个 |
| `.skip(n)` | 跳过前 n 个 |
| `.chain(另一个)` | 把两个迭代器首尾相接 |

`enumerate` 特别常用，想要下标就用它：

```rust
let names = vec!["Zhang", "Li", "Wang"];
for (i, name) in names.iter().enumerate() {
    println!("第 {} 个是 {}", i, name);
}
```

## 一个完整的流水线例子

把上面这些串起来，感受一下链式调用的爽：

```rust
fn main() {
    let sum: i32 = (1..=5)          // 区间迭代器：1,2,3,4,5
        .map(|x| x * 2)              // 2,4,6,8,10
        .filter(|x| x > 5)           // 6,8,10
        .sum();                      // 消费掉，求和 = 24
    println!("sum = {}", sum);
}
```

运行上面命令看看输出：`sum = 24`。

## 零成本抽象：和手写循环一样快

新手会担心「这么多层函数调用，会不会很慢？」。Rust 的答案是：**不会**。

迭代器及其适配器在编译期会被单态化、内联优化，最终生成的机器码和你手写 `for` 循环、手动 `push` 到 Vec 几乎一模一样，甚至有时因为编译器能自动向量化而更快。这就是 Rust 招牌的**零成本抽象**：写起来像高级函数式，跑起来像底层手写。

所以 Rust 社区的偏好是：能用迭代器流水线就别写嵌套循环，代码聚焦「做什么」，而不是「怎么挪游标」。

## 今日小结

- 迭代器惰性求值：创建不干活，调用 `next()` 或接消费器才真正遍历
- 三种创建方式：`iter()`（引用）/ `iter_mut()`（可变引用）/ `into_iter()`（所有权）
- 消费适配器：`sum()`、`collect()` 等，把迭代器变成具体值
- 迭代适配器：`map` / `filter` / `enumerate` / `zip` 等，返回新迭代器，惰性
- 链式流水线 `iter().map().filter().collect()` 是 Rust 函数式风格核心
- 零成本抽象：性能等同手写循环

## 动手任务

1. 用迭代器把 `vec![1,2,3,4,5,6]` 里的偶数挑出来、各自平方，收集成新 Vec 打印。
2. 用 `enumerate()` 遍历一个字符串数组，打印「下标: 字符串」。
3. 把一段手写 `for` 循环（比如累加所有正数）改写成 `iter().filter().sum()` 版本，对比一下行数。

> `collect()` 报 type annotations needed？在它后面加 `::<Vec<i32>>()`  turbofish 标注试试。

**配合阅读**：[官方书·第13章 使用迭代器处理元素序列](https://rustwiki.org/zh-CN/book/ch13-02-iterators.html)
