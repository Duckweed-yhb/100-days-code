# Day39 共享状态与 Mutex

> 目标：学会用 `Mutex<T>` 保护多线程共享的可变数据，掌握 `Arc<Mutex<T>>` 这个经典组合，并认识 `Send` / `Sync` 两个标记 trait。

## 共享状态：另一条路

Day38 的 channel 是「发消息共享数据」。但有时候就是要让多个线程**同时读写同一块内存**——比如一个全局计数器、一个共享缓存。这时候用的是**共享状态**，核心工具是互斥器 `Mutex<T>`。

互斥器（mutual exclusion，mutex）的规矩很简单：**同一时刻只允许一个线程访问数据**。谁要用数据，先拿锁；用完了，锁自动放给别人。

## 用 Mutex

```rust
use std::sync::Mutex;

fn main() {
    let m = Mutex::new(5); // 把 5 包进互斥器

    {
        let mut num = m.lock().unwrap(); // 拿锁，拿不到就阻塞等
        *num = 6;
    } // num（一个 MutexGuard）离开作用域，锁自动释放

    println!("m = {:?}", m); // Mutex { data: 6 }
}
```

两个关键点：

- `lock()` 返回一个 `MutexGuard`（守卫），它实现了 `Deref`，你可以像操作普通可变引用一样 `*num = 6`。
- 这个守卫**离开作用域时自动 drop，锁自动释放**——和所有权/借用规则结合，根本不用担心「忘了解锁」。

## 多线程共享计数：Arc<Mutex<T>>

经典问题：开 10 个线程，每个都给同一个计数器 +1，最后结果应该是 10。

难点在于：多个线程要**共享同一个 Mutex**，但 `Mutex` 的所有权只能移动一次；用 `Rc` 又会编译报错——`Rc` 不是线程安全的。答案是 Day34 提过的 `Arc<T>`（原子引用计数）。

经典组合：**`Arc<Mutex<T>>`**。

```rust
use std::sync::{Arc, Mutex};
use std::thread;

fn main() {
    // Arc 包 Mutex：多线程共享所有权 + 互斥访问内部值
    let counter = Arc::new(Mutex::new(0));
    let mut handles = vec![];

    for _ in 0..10 {
        // 每个线程克隆一份 Arc，引用计数 +1，不拷贝数据
        let counter = Arc::clone(&counter);
        let handle = thread::spawn(move || {
            let mut num = counter.lock().unwrap(); // 拿锁
            *num += 1;                             // 改
        }); // num 离开作用域自动解锁
        handles.push(handle);
    }

    for handle in handles {
        handle.join().unwrap(); // 等所有线程跑完
    }

    // 最后拿锁读出结果
    println!("结果: {}", *counter.lock().unwrap()); // 10
}
```

运行上面命令看看输出：`结果: 10`。没有 `Arc<Mutex>`，这 10 个线程抢计数器就会出现竞争条件，结果不可预测。

> 记忆配对再出现一次：单线程多所有者可改 = `Rc<RefCell<T>>`；多线程多所有者可改 = `Arc<Mutex<T>>`。结构一模一样，只是把 `Rc→Arc`、`RefCell→Mutex`。

## Send 与 Sync：两个标记 trait

Rust 的线程安全不是靠人自觉，而是靠两个内嵌在语言里的**标记 trait**（它们没有任何方法，只是给编译器打个标签）：

| trait | 含义 |
|---|---|
| `Send` | 这个类型的所有权可以安全地**转移**到另一个线程 |
| `Sync` | 这个类型的引用 `&T` 可以安全地**共享**给多个线程（等价于 `&T: Send`） |

规则：

- 几乎所有基本类型都是 `Send + Sync`；一个类型的所有字段都 `Send`/`Sync`，它自动也是。
- `Rc<T>`、`RefCell<T>` **不是** `Send`/`Sync`（单线程专用），所以前面你用 `Rc` 跨线程会编译报错——这正是保护机制。
- `Arc<T>`、`Mutex<T>` 是 `Send + Sync`，能跨线程。
- 手动实现 `Send`/`Sync` 属于 `unsafe` 操作（要自己保证真的线程安全），一般用标准库自动推导就够了。

## 锁的常见坑

- **别长时间持锁**：持锁期间别做 IO、别再去拿别的锁，容易死锁。
- **注意死锁**：两个线程以不同顺序拿两把锁，可能互相等。保持一致的加锁顺序。
- `lock()` 返回 `Result`：如果别的线程持锁时 panic 了，锁会变成「中毒」状态，这里我们简单 `.unwrap()` 即可。

## 今日小结

- `Mutex<T>` 保证同一时刻只有一个线程访问数据；`lock()` 返回的 `MutexGuard` 离开作用域自动解锁
- 多线程共享同一个可变数据，用 `Arc<Mutex<T>>`：Arc 管多所有者，Mutex 管互斥
- `Arc::clone(&counter)` 只增引用计数不拷贝数据
- `Send`（所有权可跨线程转移）、`Sync`（引用可跨线程共享）是编译器自动推导的标记 trait
- `Rc`/`RefCell` 不跨线程；`Arc`/`Mutex` 跨线程

## 动手任务

1. 用 `Arc<Mutex<i32>>` 开 5 个线程，每个线程给共享计数器加 100，最后断言结果是 500。
2. 在子线程持锁期间故意再 `lock()` 一次同一个 Mutex，观察会发生什么（提示：自己等自己，死锁）。
3. 写一段代码用 `Rc` 包数据传进 `thread::spawn`，看编译器怎么提示它不是 `Send`。

> 计数器结果不对？检查每个线程里 `Arc::clone` 是否在 `move` 之前、闭包是否真的拿到了自己的克隆。

**配合阅读**：[官方书·第16章 共享状态并发](https://rustwiki.org/zh-CN/book/ch16-03-shared-state.html)
