# Day36 指针综合与循环引用

> 目标：把 Box / Rc / RefCell 串成一张全景图，理解 `Rc` 互相引用为什么会导致内存泄漏，学会用 `Weak<T>` 打破循环。

## 智能指针全家桶复习

学了三天智能指针，先把它们摆在一起对比一下：

| 类型 | 作用 | 所有权 | 借用检查 | 线程 |
|---|---|---|---|---|
| `Box<T>` | 堆分配、递归类型 | 单一 | 编译期 | 可跨线程 |
| `Rc<T>` | 引用计数，多所有者只读 | 多重 | 编译期 | **单线程** |
| `RefCell<T>` | 内部可变性 | 单一 | **运行期** | **单线程** |
| `Weak<T>` | 不拥有所有权的弱引用 | — | — | 单线程 |
| `Arc<T>` | 原子引用计数（线程安全版 Rc） | 多重 | 运行期原子计数 | 多线程 |

配套的两个 trait：

- `Deref`：让 `*` 能解引用，`Box`/`Rc`/`String`/`Vec` 都实现了它
- `Drop`：离开作用域自动清理，不能手动调，提前清理用 `std::mem::drop`

记住这个组合：单线程多所有者可改 = `Rc<RefCell<T>>`。

## 引用循环：Rust 也会内存泄漏？

Rust 宣称内存安全，但它**不保证完全没有内存泄漏**。考虑这个场景：两个 `Rc` 节点互相持有对方的强引用。

```text
   a ──Rc强引用──> b
   b ──Rc强引用──> a
```

`a` 的计数因为 `b` 指向它而永远 ≥1，`b` 的计数因为 `a` 指向它也永远 ≥1。就算外部把 `a`、`b` 都 drop 了，它们互相把对方的计数撑着，谁也不归零——这块堆内存就永远释放不掉了。这就是**引用循环（reference cycle）**导致的内存泄漏。

打个比方：两个人互相说「你的命是我的，我不能放你走」，结果谁都没法离开，一起被困住。

## Weak<T>：不持有的引用

解决办法：不是所有关系都该用强引用。**父→子**用 `Rc`（强引用，父拥有子）；**子→父**用 `Weak`（弱引用，子「知道」父在哪，但不拥有父）。

- `Weak<T>` 不增加强引用计数，只增加一个 `weak_count`，**不阻止数据被释放**
- `Rc::downgrade(&rc)` 从一个 `Rc<T>` 造出 `Weak<T>`
- `weak.upgrade()` 返回 `Option<Rc<T>>`：数据还活着就 `Some`，已经被释放就 `None`

这样父 drop 时，子对父的弱引用不阻止父释放；父释放后，子再 `upgrade()` 只会拿到 `None`，安全。

## 例子：树形结构的父子指针

树节点：父节点用强引用持有子节点，子节点用弱引用指回父节点。

```rust
use std::rc::{Rc, Weak};
use std::cell::RefCell;

#[derive(Debug)]
struct Node {
    value: i32,
    parent: RefCell<Weak<Node>>,       // 子指父：弱引用，不拥有
    children: RefCell<Vec<Rc<Node>>>, // 父指子：强引用，拥有
}

fn main() {
    // 一片叶子 leaf，暂时没有父节点
    let leaf = Rc::new(Node {
        value: 3,
        parent: RefCell::new(Weak::new()),
        children: RefCell::new(vec![]),
    });
    println!("leaf 的父 = {:?}", leaf.parent.borrow().upgrade()); // None

    // 一个分支 branch，它的孩子是 leaf
    let branch = Rc::new(Node {
        value: 5,
        parent: RefCell::new(Weak::new()),
        children: RefCell::new(vec![Rc::clone(&leaf)]), // branch 强持有 leaf
    });

    // 让 leaf 记住它的父是 branch（用弱引用，不形成循环）
    *leaf.parent.borrow_mut() = Rc::downgrade(&branch);

    println!("leaf 的父 = {:?}", leaf.parent.borrow().upgrade()); // Some(branch)
}
```

关键点：`branch -> leaf` 是强引用（父拥有子），`leaf -> branch` 是弱引用（子不拥有父）。当外部 `branch` drop 后，`leaf.parent.upgrade()` 就会变成 `None`，内存能正常回收。

## 怎么避免循环引用

设计数据结构时问自己一句：**这个关系里，谁真正「拥有」谁？**

- 拥有关系 → 用 `Rc`/`Arc`（强引用）
- 「认识但不拥有」的关系（子到父、缓存、观察者）→ 用 `Weak`

日常业务代码里循环引用不常见，主要出现在图、树、观察者模式这类结构里。知道 `Weak` 这个工具存在，真遇到时再回来查即可。

## 今日小结

- 智能指针全景：`Box`（单一堆分配）/ `Rc`（多所有者只读）/ `RefCell`（运行时借用检查）/ `Weak`（弱引用）/ `Arc`（线程安全 Rc）
- `Deref` 负责 `*` 解引用，`Drop` 负责离开作用域自动清理
- 两个 `Rc` 互相强引用会形成引用循环，导致内存永不释放
- 用 `Rc::downgrade` 造 `Weak`、`upgrade()` 拿回 `Option<Rc>` 打破循环
- 设计原则：拥有关系用强引用，「认识但不拥有」用弱引用

## 动手任务

1. 打印上面树形例子里每一步的 `Rc::strong_count` 和 `Rc::weak_count`，观察 branch 和 leaf 各自计数变化。
2. 故意构造两个 `Rc<RefCell<...>>` 互相强引用的循环，确认它们退出作用域时 `Drop` 不会被打印（内存泄漏的直观证据）。
3. 把其中一条强引用改成 `Weak`，再观察 `Drop` 是否正常触发。

> `upgrade()` 返回的是 `Option`，别直接 `.unwrap()` 在可能已释放的对象上，学会优雅处理 `None`。

**配合阅读**：[官方书·第15章 引用循环与内存泄漏](https://rustwiki.org/zh-CN/book/ch15-06-reference-cycles.html)
