# Day21 包、Crate 与模块系统

> 目标：搞清楚 Rust 怎么用「包 → crate → 模块」三层组织代码，并学会用路径、`pub`、`use` 控制谁能访问谁。

## 为什么需要模块系统

代码写到几百行后，全塞在 `main.rs` 里会变成一团乱麻：哪些是业务逻辑、哪些是工具函数、哪些是给外面用的接口，全混在一起。别的文件想用你的函数，也不知道从哪找。

Rust 用一套三层结构把代码分门别类：

- **包（package）**：一个有 `Cargo.toml` 的项目，是你 `cargo new` 出来的东西。
- **crate**：编译器编译的最小单元。一个包可以包含一个库 crate 和若干二进制 crate。
- **模块（module）**：在 crate 内部用 `mod` 划分的命名空间，可以嵌套，像文件夹一样。

把它类比成电脑文件系统：包是一个项目文件夹，crate 是里面的一个个可执行/库文件，模块就是文件夹里的子文件夹，路径就是文件路径。

## crate 根文件

Cargo 约定俗成：

| 文件 | 角色 |
|---|---|
| `src/main.rs` | 二进制 crate 根（可执行程序），必有 |
| `src/lib.rs` | 库 crate 根（可选），供别人 `use` 引用 |
| `src/bin/*.rs` | 每个文件都是一个独立的二进制 crate |

一个包**最多一个库 crate**，可以有多个二进制 crate，但至少得有一个。`cargo new my-proj` 默认只生成 `src/main.rs`；`cargo new --lib my-proj` 生成库包。

## 定义模块 `mod`

在 crate 内部用 `mod` 划分代码，模块可以嵌套：

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

## 路径：怎么找到函数

要调用模块里的函数，就得写**路径**，用 `::` 分隔每一层。有两种写法：

```rust
pub fn eat_at_restaurant() {
    // 绝对路径：从 crate 根开始，以 crate:: 开头
    crate::front_of_house::hosting::add_to_waitlist();

    // 相对路径：从当前模块开始
    front_of_house::hosting::add_to_waitlist();
}
```

| 写法 | 含义 |
|---|---|
| `crate::a::b::c` | 绝对路径，从包根开始 |
| `self::a::b` | 从当前模块开始 |
| `super::a` | 从父模块开始（类似文件系统的 `..`） |

## `pub`：默认一切私有

这里是 Rust 模块系统最反直觉、也最重要的一条规则：**Rust 默认一切都是私有的**——模块、函数、结构体、字段，全是私有的。父模块不能访问子模块的私有项（但子模块能访问父模块的私有项）。

所以上面那段代码现在编译不过，因为 `hosting` 模块和 `add_to_waitlist` 函数都是私有的。要让外面能用，必须**逐层**加 `pub`：

```rust
mod front_of_house {
    pub mod hosting {            // 模块要 pub
        pub fn add_to_waitlist() {} // 函数也要 pub
    }
}
```

注意：只把模块标 `pub` 还不够，模块内部的项仍要各自 `pub`。

结构体和枚举的可见性规则不一样：

- **结构体**标 `pub`，它的字段**默认仍私有**，要逐个字段加 `pub`。
- **枚举**标 `pub`，它的所有变体自动公开。

## `use`：把长路径引进作用域

每次都写 `crate::front_of_house::hosting::add_to_waitlist()` 太长了。`use` 就像给长路径建了个快捷方式：

```rust
use crate::front_of_house::hosting;

pub fn eat_at_restaurant() {
    hosting::add_to_waitlist(); // 直接用短名
}
```

两个惯用约定：

- **引入函数**：`use` 到父模块为止（`use ...::hosting;`），调用时写 `hosting::add_to_waitlist()`，一眼能看出这函数不是本地的。
- **引入结构体/枚举**：`use` 到完整路径（`use std::collections::HashMap;`），直接用 `HashMap::new()`。

## 重命名与嵌套

两个同名类型撞车了？用 `as` 起别名：

```rust
use std::fmt::Result;
use std::io::Result as IoResult; // 重命名避免冲突
```

从同一个父模块引入多个项，用 `{}` 合并，少写重复前缀：

```rust
use std::{cmp::Ordering, io};
use std::io::{self, Write}; // self 代表 io 本身 + Write 子项
```

`pub use` 叫**重导出（re-export）**：普通 `use` 引入的名字只有自己能用，加了 `pub` 后外部也能用这个短路径，常用于重新整理对外 API。

## 模块拆到独立文件

模块内容写多了，可以拆出去。在 crate 根写 `mod 模块名;`（注意是分号，不是大括号），Rust 就去同名文件找：

```
src/
├── front_of_house.rs      # 对应 mod front_of_house;
├── front_of_house/
│   └── hosting.rs         # 对应 pub mod hosting;
└── lib.rs
```

`src/lib.rs`：

```rust
mod front_of_house; // 从 front_of_house.rs 加载
```

`src/front_of_house.rs`：

```rust
pub mod hosting; // 从 front_of_house/hosting.rs 加载
```

模块树不变，调用代码也不用改。这就是为什么你看别人项目里 `src/` 下一堆 `.rs` 文件——它们都是通过 `mod` 声明挂到树上的。

## 今日小结

- 三层结构：包（`Cargo.toml`）→ crate（`src/main.rs` / `src/lib.rs`）→ 模块（`mod`）
- 用路径访问函数：绝对路径 `crate::a::b`，相对路径 `super::a` / `self::a`
- Rust 默认一切私有，要对外开放必须逐层加 `pub`；结构体字段默认私有，枚举变体默认公开
- `use` 把长路径引进作用域；函数引到父模块，类型引到完整路径
- `as` 重命名、`{}` 嵌套路径、`pub use` 重导出；`mod name;` 把模块拆到独立文件

## 动手任务

1. 新建一个库包 `cargo new --lib my_mod`，在 `src/lib.rs` 里写一个 `mod math { pub fn add(a: i32, b: i32) -> i32 { a + b } }`，并在别处 `use crate::math::add;` 后调用它。
2. 故意把 `pub fn add` 改成 `fn add`，观察编译报错，体会「默认私有」。
3. 把 `mod math { ... }` 拆成 `src/lib.rs` 里写 `mod math;`、内容移到 `src/math.rs`，确认还能编译。

> 提示：库包没有 `main`，可以写个 `#[cfg(test)] mod tests` 来调用，或者先不管运行，`cargo build` 不报错就算过。

**配合阅读**：[官方书·第7章 包和 crate](https://rustwiki.org/zh-CN/book/ch07-01-packages-and-crates.html)
