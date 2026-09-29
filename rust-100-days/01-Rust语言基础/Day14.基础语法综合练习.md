# Day14 基础语法综合练习

> 目标：把 Day02~13 学过的变量、类型、函数、控制流、字符串揉在一起，亲手写几个经典小程序，检查自己哪里还虚。

## 怎么用今天这篇

今天不教新知识，给你几道题。**建议先自己写，写完再看后面的参考答案。** 哪怕卡壳也先撑 15 分钟——编译报错本身就是最好的老师。

所有代码都可以放进一个 Cargo 项目的 `src/main.rs` 里跑。

---

## 热身一：判断奇偶并分级

**题目**：写一个函数 `fn classify(n: i32) -> String`，返回：

- 如果是偶数且大于等于 10：`"大偶数"`
- 如果是偶数且小于 10：`"小偶数"`
- 否则：`"奇数"`

**提示**：`n % 2 == 0` 判断奇偶；用 `if / else if / else`。

<details>
<summary>点我看参考答案</summary>

```rust
fn classify(n: i32) -> String {
    if n % 2 == 0 && n >= 10 {
        String::from("大偶数")
    } else if n % 2 == 0 {
        String::from("小偶数")
    } else {
        String::from("奇数")
    }
}

fn main() {
    println!("{}", classify(12));   // 大偶数
    println!("{}", classify(4));     // 小偶数
    println!("{}", classify(7));    // 奇数
}
```

注意每个分支返回的都是 `String`，类型一致；行尾都没加分号（它们是表达式）。
</details>

---

## 热身二：用循环累加

**题目**：计算 `1 + 2 + 3 + ... + 100` 的和并打印。

**提示**：用 `for i in 1..=100`，累加变量要 `mut`。

<details>
<summary>点我看参考答案</summary>

```rust
fn main() {
    let mut sum = 0;
    for i in 1..=100 {
        sum += i;
    }
    println!("1 到 100 的和是 {}", sum);   // 5050
}
```
</details>

---

## 小算法一：判断素数

**题目**：写一个函数 `fn is_prime(n: u64) -> bool`，判断一个数是不是素数（质数）。素数只能被 1 和它自己整除。

**思路提示**：

- 小于 2 的数都不是素数。
- 对一个数 `n`，只要在 `2` 到 `√n` 之间找到能整除它的数，就不是素数；找不到才是素数。
- 取余 `n % i == 0` 表示整除。

<details>
<summary>点我看参考答案</summary>

```rust
fn is_prime(n: u64) -> bool {
    if n < 2 {
        return false;
    }
    // 只需要检查到平方根即可：i * i <= n
    let mut i = 2;
    while i * i <= n {
        if n % i == 0 {
            return false;        // 能被 i 整除，不是素数
        }
        i += 1;
    }
    true
}

fn main() {
    for n in 1..=20 {
        if is_prime(n) {
            print!("{} ", n);    // 2 3 5 7 11 13 17 19
        }
    }
}
```

这里 `i * i <= n` 等价于"i 不超过 √n"，避免引入浮点数。
</details>

---

## 小算法二：斐波那契数列

**题目**：写一个函数 `fn fib(n: u64) -> u64`，返回第 `n` 个斐波那契数。数列规则：第 0 个是 0，第 1 个是 1，之后每个数等于前两个之和：`0, 1, 1, 2, 3, 5, 8, 13, ...`。

**思路提示**：不用递归（新手容易写得又慢又绕），用两个变量往前滚动：

- `a, b` 一开始是 `0, 1`。
- 每走一步：新数 = `a + b`，然后把 `a` 换成旧的 `b`，`b` 换成新数。

<details>
<summary>点我看参考答案</summary>

```rust
fn fib(n: u64) -> u64 {
    let mut a = 0;          // 第 0 个
    let mut b = 1;          // 第 1 个
    for _ in 0..n {
        let next = a + b;
        a = b;
        b = next;
    }
    a
}

fn main() {
    for i in 0..=10 {
        print!("{} ", fib(i));   // 0 1 1 2 3 5 8 13 21 34 55
    }
}
```

`for _ in 0..n` 里的 `_` 表示"这个循环变量我用不到"，只是重复 `n` 次。
</details>

---

## 字符串小练：统计字符

**题目**：给定字符串 `"hello rust"`，统计里面有几个 `'l'`。

**提示**：用 `for c in s.chars()` 遍历，用计数器累加。

<details>
<summary>点我看参考答案</summary>

```rust
fn main() {
    let s = "hello rust";
    let mut count = 0;
    for c in s.chars() {
        if c == 'l' {
            count += 1;
        }
    }
    println!("l 出现了 {} 次", count);   // 2
}
```
</details>

---

## 今日小结

- 综合题就是把"变量 + 类型 + 函数 + if/for + 字符串"串起来用，没有新语法。
- 素数：用 `while i*i <= n` + `%` 取余判断，效率比从 2 查到 n 高很多。
- 斐波那契：用两个变量滚动迭代，比递归直观、高效。
- 遍历字符串用 `.chars()`，遍历区间用 `a..=b`，计数器记得 `mut`。

## 动手任务

1. 把上面五道题都自己敲一遍跑通，至少做到不看答案能写出 `is_prime` 和 `fib`。
2. 扩展 `is_prime`：在 `main` 里打印 1~100 之间所有素数。
3. 挑战：写一个函数 `fn is_palindrome(s: &str) -> bool`，判断字符串正着读反着读是否一样（比如 `"abcba"`）。提示：可以先把字符收集起来，再首尾比较。（做不出没关系，Day15 项目会用到字符串处理。）

**配合阅读**：[官方书·第3章 常见编程概念（复习）](https://rustwiki.org/zh-CN/book/ch03-05-control-flow.html)
