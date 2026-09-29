# Day35 RefCell 与内部可变性

> 目标：理解「外部看着不可变，内部却能改」这种内部可变性模式，学会用 `RefCell<T>` 把借用检查从编译期推迟到运行时。

## 编译期借用检查 vs 运行期借用检查

我们早就学过借用规则：同一时刻，要么多个不可变借用，要么一个可变借用，不能兼得。这套规则是**编译期**由借用检查器强制的。

但有些场景，编译期实在判断不出来，我们自己却能保证不违规。比如：「外部拿到的是一个不可变引用，但我想在某个方法里偷偷改一下内部缓存」。这时候就需要 `RefCell<T>`。

`RefCell<T>` 的规则：它同样强制「同一时刻要么多个不可变借用、要么一个可变借用」，**只是把检查时机从编译期挪到了运行期**。如果你违反了，不会编译报错，而是运行时 `panic!` 崩给你看。

打个比方：编译期借用检查像机场登机口扫码，你还没进门就被拦下；`RefCell` 像上了飞机才有人查票——规则没变，但查晚了。所以它适合「我逻辑上确定不会冲突，但编译器看不出来」的场景。

| 类型 | 所有权 | 借用检查时机 |
|---|---|---|
| `Box<T>` | 单一 | 编译期 |
| `Rc<T>` | 多重 | 编译期（只允许不可变） |
| `RefCell<T>` | 单一 | **运行时**（可变不可变都行） |

> `RefCell<T>` 也只能在**单线程**用，多线程版是 `Mutex<T>`（Day39）。

## 怎么用 RefCell

两个关键方法：

- `borrow()`：返回一个 `Ref<T>` 智能指针，代表不可变借用，可同时存在多个
- `borrow_mut()`：返回一个 `RefMut<T>`，代表可变借用，同一时刻只能有一个

```rust
use std::cell::RefCell;

fn main() {
    let cell = RefCell::new(vec![1, 2, 3]);

    // 注意：cell 本身是不可变绑定 let，照样能改内部！
    cell.borrow_mut().push(4); // 可变借用，往里塞一个 4
    println!("{:?}", cell.borrow()); // 不可变借用，读出来看看
}
```

运行上面命令看看输出：`[1, 2, 3, 4]`。重点体会——`cell` 前面是 `let`（不可变），我们却改了它内部，这就是**内部可变性（interior mutability）**。

## 违反规则会运行时崩溃

借用规则一条都没少，只是延后了。如果你在同一时刻借出两个可变借用，运行时会 panic：

```rust
use std::cell::RefCell;

fn main() {
    let cell = RefCell::new(String::from("hello"));
    let r1 = cell.borrow_mut(); // 第一个可变借用
    // let r2 = cell.borrow_mut(); // ❌ 运行时 panic: BorrowMutError
    println!("{}", r1);
}
```

同理，不可变借用还活着时就去拿可变借用也会 panic。所以用 `RefCell` 要自己保证逻辑正确，把它当成「我确定不会冲突，编译器帮我在运行时兜底查一下」。

## 黄金组合：Rc<RefCell<T>>

`Rc` 能多所有者共享，但只读；`RefCell` 能内部可变，但单一所有者。把两者套起来，就得到了「**多个所有者，还能修改同一份数据**」：

```rust
use std::rc::Rc;
use std::cell::RefCell;

fn main() {
    // 一份数据，包在 RefCell 里，再被 Rc 共享
    let value = Rc::new(RefCell::new(5));

    let a = Rc::new(Rc::clone(&value)); // a 共享 value
    let _b = Rc::clone(&a);             // _b 也共享同一份

    // 透过 Rc 拿到 RefCell，再 borrow_mut 改内部
    *value.borrow_mut() += 10;

    println!("value = {}", value.borrow()); // 15
    println!("a 里看到的也是 {}", a.borrow()); // 15，同一份数据
}
```

记忆配对（后面学多线程会再见到一次）：

- 单线程、多所有者、可修改 → `Rc<RefCell<T>>`
- 多线程、多所有者、可修改 → `Arc<Mutex<T>>`（Day39）

## 什么时候该用，什么时候别用

适合用：

- 内部缓存、计数器，外部接口却是不可变引用
- 图/树结构里多个节点互相共享且需要改
- 你确定运行时不会同时拿到冲突借用

别滥用：它把编译期的安全保证推迟成运行时 panic，能在编译期查出问题就别用它。

## 今日小结

- `RefCell<T>` 把借用检查从编译期推迟到运行期，违反规则会 panic 而不是编译报错
- 它实现**内部可变性**：外部 `let` 不可变，内部照样能改
- 两个方法：`borrow()`（不可变，可多个）、`borrow_mut()`（可变，只能一个）
- 黄金组合 `Rc<RefCell<T>>` = 多所有者 + 可修改（单线程）
- 多线程对应物是 `Arc<Mutex<T>>`；RefCell 不能跨线程

## 动手任务

1. 用 `RefCell<Vec<i32>>`，在一个不可变绑定上调用 `borrow_mut().push(99)`，再 `borrow()` 读出来。
2. 故意写一段同时 `borrow_mut()` 两次的代码，运行观察 `BorrowMutError` panic 信息。
3. 用 `Rc<RefCell<i32>>` 创建两个共享同一计数的变量，通过其中一个 +1，确认另一个也看到变化。

> 忘了 `use std::cell::RefCell;`？编译器会提示你加导入。

**配合阅读**：[官方书·第15章 RefCell\<T\> 与内部可变性](https://rustwiki.org/zh-CN/book/ch15-05-interior-mutability.html)
