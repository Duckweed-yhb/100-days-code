# Day21 包、Crate 与模块系统

> **前置知识**：Day02 第一个 Rust 程序与 Cargo（`cargo new` 出来的目录长什么样）、Day06 函数（`fn` 和 `pub`）
> **难度**：入门
> **预计阅读时间**：25 分钟
> **代码说明**：本章大部分代码放在**同一个** `main.rs` 里，每块都带完整的 `fn main()`，单独复制就能跑。
> 最后一节讲「把模块拆到独立文件」，那两块标了 `⚠️`，它们是**别的文件**里的内容，不能抄进 `main.rs`。
> 每块前面标了 `📦`（能单独跑）、`🧩`（依赖上面的块）或 `⚠️`（给你看的，抄不了）。

---

## 为什么需要模块系统

代码写到几百行，全塞在 `main.rs` 里就乱了：哪些是业务逻辑、哪些是工具函数、哪些是给外面用的接口，混成一团。别的文件想用你的函数，也不知道从哪找。

Rust 用三层结构把代码分门别类：

- **包（package）**：一个有 `Cargo.toml` 的项目，也就是 `cargo new` 出来的那个目录。
- **crate**：编译器一次编译的单元。一个包里可以有一个库 crate，外加若干个二进制 crate。
- **模块（module）**：crate 内部用 `mod` 划出来的命名空间，可以嵌套。

拿电脑里的文件夹打比方：包是一个项目文件夹，crate 是里面的可执行文件和库文件，模块是子文件夹，路径就是文件路径。

## crate 根文件

Cargo 认这几个约定：

| 文件 | 角色 |
|---|---|
| `src/main.rs` | 二进制 crate 的根，产出可执行程序 |
| `src/lib.rs` | 库 crate 的根，供别人 `use` |
| `src/bin/*.rs` | 每个文件都是一个独立的二进制 crate |

一个包里**最多一个库 crate**，二进制 crate 可以有多个，但整个包至少得有一个 crate。`cargo new my-proj` 只生成 `src/main.rs`；`cargo new --lib my-proj` 生成的是库包。

## 定义模块 mod

`mod 名字 { ... }` 就在 crate 里划出一块命名空间，里面还能继续套 `mod`：

📦 本块能单独跑（`main` 里先空着，今天先看模块树怎么搭）：

```rust
mod front_of_house {
    mod hosting {
        fn add_to_waitlist() {}
        fn seat_at_table() {}
    }

    mod serving {
        fn take_order() {}
    }
}

fn main() {
    // 先不调用，这一步只搭结构
}
```

这棵模块树长这样：

```
crate
└── front_of_house
    ├── hosting
    │   ├── add_to_waitlist
    │   └── seat_at_table
    └── serving
        └── take_order
```

## 路径

要调用模块里的函数，得写**路径**，每层之间用 `::` 隔开。`::` 读作「里面的」：`crate::a::b` 就是「crate 里面的 a 里面的 b」。

按出发点的不同，路径分几种：

| 写法 | 含义 |
|---|---|
| `crate::a::b::c` | 绝对路径，从 crate 根出发 |
| `self::a::b` | 相对路径，从当前模块出发 |
| `super::a` | 相对路径，从父模块出发（类似文件系统里的 `..`） |

先看一个调用不成的版本，正好把「默认私有」这条规则引出来。

⚠️ 本块编译不过，贴出来给你看报错长什么样：

```rust
mod front_of_house {
    mod hosting {
        fn add_to_waitlist() {}
    }
}

fn main() {
    crate::front_of_house::hosting::add_to_waitlist();
}
```

编译器原话：

```
error[E0603]: module `hosting` is private
 --> main.rs:8:28
  |
8 |     crate::front_of_house::hosting::add_to_waitlist();
  |                            ^^^^^^^  --------------- function `add_to_waitlist` is not publicly re-exported
  |                            |
  |                            private module
  |
note: the module `hosting` is defined here
 --> main.rs:2:5
  |
2 |     mod hosting {
  |     ^^^^^^^^^^^
```

路径本身没写错。问题是 `hosting` 是私有的，`main` 所在的 crate 根够不着它。

## pub 与默认私有

Rust 模块系统里最需要先记住的一条规则：**默认一切都是私有的**。模块、函数、结构体、字段，全都是。父模块不能访问子模块的私有项，反过来子模块可以访问父模块的私有项。

要让外面能用，就得**逐层**加 `pub`。下面这版把该开的门都开了，`main` 里直接调用了它：

📦 本块能单独跑：

```rust
mod front_of_house {
    pub mod hosting {
        pub fn add_to_waitlist() {
            println!("已加入等位名单");
        }
    }

    pub mod serving {
        pub fn take_order() {
            println!("下了单");
        }
    }
}

fn main() {
    // 绝对路径：从 crate 根出发
    crate::front_of_house::hosting::add_to_waitlist();

    // 相对路径：当前就在 crate 根
    front_of_house::hosting::add_to_waitlist();

    crate::front_of_house::serving::take_order();
}
```

注意「逐层」两个字：只把 `hosting` 标成 `pub` 还不够，里面的 `add_to_waitlist` 也必须自己是 `pub`，否则外面照样调不到。

结构体和枚举的可见性规则不太一样：

- **结构体**标 `pub` 之后，字段**默认还是私有**的，要逐个字段加 `pub`。
- **枚举**标 `pub` 之后，所有变体跟着一起公开。

## self 和 super

`self::` 就是从当前模块出发，`super::` 就是从父模块出发。同一个 crate 内部互相调用时会用到：

📦 本块能单独跑：

```rust
mod front_of_house {
    pub mod hosting {
        pub fn add_to_waitlist() {
            println!("已加入等位名单");
        }
    }

    pub mod serving {
        pub fn take_order() {
            println!("下了单");
        }

        pub fn eat_here() {
            // self:: 从当前模块出发，就是 serving::take_order
            self::take_order();
            // super:: 从父模块出发，就是 front_of_house::hosting::add_to_waitlist
            super::hosting::add_to_waitlist();
        }
    }
}

fn main() {
    crate::front_of_house::serving::eat_here();
}
```

## use 建个短名

每次都写 `crate::front_of_house::hosting::add_to_waitlist()` 太长。`use` 相当于给这条路径建了个快捷方式：

📦 本块能单独跑：

```rust
mod front_of_house {
    pub mod hosting {
        pub fn add_to_waitlist() {
            println!("已加入等位名单");
        }
    }
}

// 把 hosting 引进当前作用域
use crate::front_of_house::hosting;

fn main() {
    hosting::add_to_waitlist(); // 直接用短名
}
```

两个惯用约定：

- **引入函数**：`use` 到父模块为止（`use ...::hosting;`），调用时写 `hosting::add_to_waitlist()`。这样一眼能看出这函数不是本模块的。
- **引入结构体或枚举**：`use` 到完整路径（`use std::collections::HashMap;`），直接用 `HashMap::new()`。

## 重命名与嵌套

两个同名类型撞车，用 `as` 起别名：

📦 本块能单独跑：

```rust
use std::fmt::Result;
use std::io::Result as IoResult; // 重命名，避开同名冲突

fn main() {
    let _a: Result = Ok(());
    let _b: IoResult<()> = Ok(());
}
```

从同一个父模块引入多个项，用 `{}` 合并，前缀只写一遍：

📦 本块能单独跑：

```rust
use std::{cmp::Ordering, io}; // 前缀 std:: 只写一次
use std::io::Write;           // io 上面已经引进了，这里只补一个 Write

fn main() {
    println!("{:?}", Ordering::Less);
}
```

`{}` 里还能写 `self`，表示父模块本身：

```text
use std::io::{self, Read, Write};   // self 就是 io 本身

// 上面这一句等价于下面两句：
use std::io;
use std::io::{Read, Write};
```

这里有个坑：`use std::{cmp::Ordering, io};` 和 `use std::io::{self, Write};` 不能同时写，`io` 会被定义两遍，报 `error[E0252]: the name 'io' is defined multiple times`。要么合成一句，要么把 `self` 去掉、只引它里面的项。

`pub use` 叫**重导出（re-export）**：普通 `use` 引进来的名字只有自己能用，加了 `pub` 之后外部也能用这个短路径，常用来整理对外的 API。

## 模块拆到独立文件

模块内容写多了可以拆出去。在 crate 根写 `mod 模块名;`，注意结尾是**分号**，不是大括号。Rust 会去同名文件里找内容：

```
src/
├── front_of_house.rs      # 对应 mod front_of_house;
├── front_of_house/
│   └── hosting.rs         # 对应 pub mod hosting;
└── lib.rs
```

`src/lib.rs` 里这样写：

⚠️ 本块是 `src/lib.rs` 里的内容，放进 `main.rs` 会编不过（Rust 会去找一个不存在的 `front_of_house.rs`）：

```rust
mod front_of_house; // 从 front_of_house.rs 加载内容
```

`src/front_of_house.rs` 里这样写：

⚠️ 本块是 `src/front_of_house.rs` 里的内容，同样不能放进 `main.rs`：

```rust
pub mod hosting; // 从 front_of_house/hosting.rs 加载内容
```

拆成文件之后模块树没变，调用代码也不用改。这就是为什么别人项目里 `src/` 下面一堆 `.rs` 文件，它们都是通过 `mod` 声明挂到树上的。

## 今日小结

- 三层结构：包（`Cargo.toml`）→ crate（`src/main.rs` / `src/lib.rs`）→ 模块（`mod`）
- 用路径访问函数：绝对路径 `crate::a::b`，相对路径 `self::a` / `super::a`
- Rust 默认一切私有，对外开放必须逐层加 `pub`；结构体字段默认私有，枚举变体默认公开
- `use` 把长路径引进作用域；函数引到父模块，类型引到完整路径
- `as` 重命名、`{}` 嵌套路径、`self` 表示父模块本身、`pub use` 重导出
- `mod 名字;`（分号）把模块拆到独立文件，文件名要和模块名对上

## 动手任务

1. `cargo new --lib my_mod`，在 `src/lib.rs` 里写 `mod math { pub fn add(a: i32, b: i32) -> i32 { a + b } }`，再写一个 `pub fn demo() -> i32`，在里面 `use crate::math::add;` 之后调用它。库包没有 `main`，跑不了 `cargo run`，`cargo build` 通过就算完成。
2. 把 `pub fn add` 改成 `fn add`，重新 `cargo build`，看报什么错，体会「默认私有」。
3. 把 `mod math { ... }` 拆开：`src/lib.rs` 里只留 `mod math;`，内容挪到 `src/math.rs`，再 build 一次，确认还能通过。

> 第 1 题提示：`cargo build` 过了就算完成。库包没有 `main`，`cargo run` 会报找不到 main 函数。
> 第 2 题提示：报的错是 `E0603`，和前面那个「hosting is private」是同一类，只不过这次私有的是一个函数。

**配合阅读**：[官方书·第7章 包和 crate](https://rustwiki.org/zh-CN/book/ch07-01-packages-and-crates.html)
