# Day33 智能指针 Box

> 目标：学会用 `Box<T>` 把数据放到堆上，理解递归类型为什么必须靠它，并认识 `Deref`（解引用）和 `Drop`（自动清理）这两个让智能指针「智能」起来的 trait。

## 栈和堆回顾

我们在 Day10 讲过所有权，这里再快速回顾一下内存布局：

- **栈（stack）**：大小固定、后进先出，放函数参数、局部变量，很快
- **堆（heap）**：大小动态、要分配和回收，放大块、运行时才知道大小的数据

普通变量默认都在栈上。但有些时候你就是想把数据放到堆上——这就是 `Box<T>` 干的事。

## Box<T>：把值放进堆里

`Box<T>` 叫「箱式指针」。它做的事很简单：**在堆上分配一块内存存 T，然后在栈上留一个指向这块内存的指针**。它本身几乎没有额外性能开销。

```rust
fn main() {
    let b = Box::new(5);  // 5 存在堆上，栈上的 b 只是个指针
    println!("b = {}", b); // 用起来和普通 i32 几乎没区别
}
```

什么时候要用 `Box`？官方书给了三个典型场景：

1. **编译时大小未知的递归类型**（比如链表）——最重要，下面详讲
2. 转移大数据所有权时，避免把整份数据从栈上拷来拷去（只拷一个指针）
3. 只关心某个类型实现了某个 trait，而不关心具体类型（trait 对象，Day41 讲）

## 递归类型为什么必须用 Box

这是 `Box` 最经典的用途。我们想写一个链表：每个节点存一个整数和下一个节点。直觉上写成这样：

```rust
// ❌ 编译错误：recursive type has infinite size（递归类型大小无限）
// enum List {
//     Cons(i32, List),
//     Nil,
// }
```

为什么报错？因为 Rust 在编译时必须知道每个类型占多少字节。`Cons(i32, List)` 里又嵌了一个 `List`，`List` 里又是 `Cons`……这样无限套下去，大小算不出来。

解决办法是在中间插一层指针。指针本身大小是固定的（64 位机器上 8 字节），不管它指向的东西多大：

```rust
// ✅ 用 Box<List> 打断无限递归
enum List {
    Cons(i32, Box<List>), // Box 是指针，大小已知
    Nil,
}

use List::{Cons, Nil};

fn main() {
    // 1 -> 2 -> 3 -> Nil
    let list = Cons(1, Box::new(Cons(2, Box::new(Cons(3, Box::new(Nil))))));
}
```

打个比方：递归类型像「套娃」，直接套下去无穷无尽，编译器算不出总尺寸；塞一个 `Box` 进去相当于在两层套娃之间放一张「下一层在那边」的纸条，纸条本身大小固定，编译器就能算了。

## Deref trait：让 * 能解引用

普通指针 `&T` 可以用 `*` 解引用。`Box<T>` 也可以，背后靠的是 `std::ops::Deref` trait。我们自己实现一个迷你 `MyBox` 看看：

```rust
use std::ops::Deref;

struct MyBox<T>(T); // 元组结构体，包着一个 T

impl<T> MyBox<T> {
    fn new(x: T) -> MyBox<T> {
        MyBox(x)
    }
}

// 实现 Deref：告诉编译器 *y 应该怎么取到底层值
impl<T> Deref for MyBox<T> {
    type Target = T; // 解引用后得到的类型是 T
    fn deref(&self) -> &T {
        &self.0 // 返回内部数据的引用
    }
}

fn main() {
    let x = 5;
    let y = MyBox::new(x);
    assert_eq!(5, x);
    assert_eq!(5, *y); // 底层自动展开成 *(y.deref())
}
```

### 解引用强制多态（deref coercion）

更妙的是：传参时编译器会**自动**连续调用 `deref()`，帮你把类型一层层转过去，不用手写 `*`：

```rust
fn hello(name: &str) {
    println!("Hello, {}!", name);
}

fn main() {
    let m = MyBox::new(String::from("Rust"));
    // m 的类型是 MyBox<String>，但 hello 要 &str
    // 编译器自动：&MyBox<String> -> &String -> &str
    hello(&m);
}
```

这个机制你以后写库会经常「无感地」用到——`String` 能传给要 `&str` 的函数，就是它在背后工作。

## Drop trait：离开作用域自动清理

`Box` 在离开作用域时，会自动把它在堆上分配的内存释放掉。这个自动清理靠的是 `Drop` trait：值一离开作用域，编译器就自动调它的 `drop` 方法。

```rust
struct CustomSmartPointer {
    data: String,
}

impl Drop for CustomSmartPointer {
    fn drop(&mut self) {
        // 离开作用域时自动打印，用来观察清理时机
        println!("正在丢弃 data=`{}`", self.data);
    }
}

fn main() {
    let _c = CustomSmartPointer { data: String::from("hello") };
    println!("main 中间");
    // main 结束时，_c 离开作用域，自动调用 drop
}
```

注意两个细节：

- 清理顺序是**后创建的先 drop**（栈式 LIFO）。
- 你**不能手动调 `c.drop()`**，那会导致「双重释放」。想提前释放，用标准库函数 `std::mem::drop`：

```rust
fn main() {
    let c = CustomSmartPointer { data: String::from("hi") };
    drop(c); // 提前丢弃：把所有权交给 drop 函数
    println!("main 结束"); // 这行之前 c 已经被释放
}
```

> 这就是 Rust 不需要垃圾回收也不内存泄漏的关键之一：所有权 + Drop 在编译期就安排好了清理时机。

## 今日小结

- `Box<T>` 把数据分配到堆上，栈上只留指针，几乎零开销
- 递归类型（链表等）编译时算不出大小，必须用 `Box<...>` 插一层固定大小的指针来打断递归
- 实现 `Deref` 后，自定义类型也能用 `*` 解引用；解引用强制多态让 `&Box<String>` 自动当 `&str` 传
- 实现 `Drop` 后，值离开作用域自动清理；不能手动调 `drop()`，提前清理用 `std::mem::drop(x)`
- `String`、`Vec<T>` 其实也是智能指针，Box/Rc/RefCell 是今天开始认识的新朋友

## 动手任务

1. 用 `Box` 实现一个简单的链表 `List`，写一个方法把所有元素加起来（提示：递归 match）。
2. 给你的 `CustomSmartPointer` 实现 `Drop`，在 `main` 里创建两个实例，观察它们 drop 的先后顺序。
3. 写一个自己的 `MyBox`，实现 `Deref`，然后验证 `hello(&mybox_string)` 能自动转成 `&str`。

> 递归类型报错 `recursive type has infinite size`？检查枚举变体里是不是直接嵌了自身，套个 `Box` 试试。

**配合阅读**：[官方书·第15章 智能指针 Box\<T\>](https://rustwiki.org/zh-CN/book/ch15-01-box.html)
