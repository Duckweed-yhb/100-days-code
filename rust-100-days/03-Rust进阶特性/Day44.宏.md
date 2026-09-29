# Day44 宏

> 目标：知道宏是「写代码来生成代码」，会读 `macro_rules!` 声明宏（甚至能手写一个迷你 `vec!`），并认识三种过程宏大致长什么样。

## 宏和函数有什么不一样

你早就在用宏：`println!`、`vec!`、`assert_eq!` 后面都带个 `!`。宏（macro）是**元编程**——让你写一段代码，它在编译期展开成更多代码。

和函数比：

| | 函数 | 宏 |
|---|---|---|
| 参数数量 | 固定 | **可变数量**（`vec![1,2,3]` 随便几个） |
| 展开时机 | 运行时调用 | **编译期展开** |
| 能力 | 普通逻辑 | 能在类型上自动 impl trait 等 |
| 缺点 | 简单 | 更难写、难维护，必须先定义后用 |

## 声明宏 macro_rules!：复刻 vec!

`vec!` 是最经典的声明宏。它像 `match` 一样：你给它一组模式，它匹配你的输入，然后展开成对应代码。

我们手写一个迷你版 `vec!`：

```rust
// macro_rules! 定义宏
macro_rules! my_vec {
    // 模式：匹配 0 个或多个表达式，用逗号分隔
    ( $( $x:expr ),* ) => {
        {
            let mut temp_vec = Vec::new();
            $(
                temp_vec.push($x); // 对每个 $x 都生成一行 push
            )*
            temp_vec // 整个块的值就是这个 Vec
        }
    };
}

fn main() {
    let v = my_vec![1, 2, 3]; // 展开成 push(1); push(2); push(3);
    assert_eq!(v, vec![1, 2, 3]);
    println!("{:?}", v);
}
```

语法解读：

- `$x:expr`：把一段表达式绑到变量 `x` 上（`expr` 是片段分类符，表示一个表达式）
- `$( ... ),*`：括号里的东西重复零到多次，每次之间用逗号
- `$( temp_vec.push($x); )*`：对重复出来的每个 `$x`，都生成一行 push

运行上面命令看看输出：`[1, 2, 3]`。`my_vec![1,2,3]` 在编译期就被展开成了手动 push 三次的代码——和你自己写的一模一样，但调用方爽多了。

## 三种过程宏

声明宏是「按模式替换」。还有一类更强大的**过程宏（procedural macro）**：它接收一段 Rust 代码（token 流），像函数一样处理后吐出新的代码。过程宏必须放在一个独立的、声明了 `proc-macro = true` 的 crate 里，常用 `syn`（解析代码）和 `quote`（生成代码）两个 crate。

三种过程宏：

| 种类 | 写法示例 | 干什么 |
|---|---|---|
| 自定义 derive | `#[derive(HelloMacro)]` | 给 struct/enum 自动 impl trait（你天天见的 `#[derive(Debug)]`） |
| 属性宏 | `#[route(GET, "/")]` | 定义可挂在函数/模块上的新属性 |
| 函数宏 | `sql!(SELECT * FROM ...)` | 长得像函数调用，处理括号里的代码 |

### derive 宏长什么样

你写过无数次 `#[derive(Debug)]`，它就是个 derive 宏，自动帮你 impl `Debug`。一个最小的 derive 宏骨架：

```rust
use proc_macro::TokenStream;
use quote::quote;

#[proc_macro_derive(HelloMacro)]
pub fn hello_macro_derive(input: TokenStream) -> TokenStream {
    let ast = syn::parse(input).unwrap(); // 把输入解析成语法树
    let name = &ast.ident;                // 拿到类型名，比如 Pancakes
    let gen = quote! {
        impl HelloMacro for #name {
            fn hello_macro() {
                println!("Hello, Macro! 我叫 {}", stringify!(#name));
            }
        }
    };
    gen.into()
}
```

用的时候只要：

```rust
#[derive(HelloMacro)]
struct Pancakes;

fn main() {
    Pancakes::hello_macro(); // 自动 impl 出来的方法
}
```

## 今天要掌握到什么程度

宏是 Rust 进阶里的「高级玩具」，应用开发中你大多是**用别人写的宏**（`println!`、`vec!`、`#[derive(...)]`），而不是自己造宏。今天的目标：

1. 看得懂 `macro_rules!` 里 `$x:expr`、`$()*` 是啥意思
2. 知道过程宏分 derive / 属性 / 函数三种
3. 真要写库时再去翻详细文档

## 今日小结

- 宏是编译期展开的元编程，支持可变参数，比函数能力更强
- 声明宏 `macro_rules!` 按模式匹配输入再展开，`vec!` 就是这么实现的
- `$x:expr` 匹配表达式片段，`$()*` 表示重复多次
- 过程宏分三类：自定义 derive（`#[derive]`）、属性宏、函数宏，需独立 crate
- 日常多是「用宏」，不是「写宏」；认得语法即可

## 动手任务

1. 把上面的 `my_vec!` 例子敲一遍跑通，试试空参数 `my_vec![]` 能不能工作。
2. 给 `my_vec!` 加一个支持 `my_vec![0; 3]`（像标准库那样表示「0 重复 3 次」）的分支。
3. 在你已经写过的结构体上数数：你用过几种 `#[derive(...)]`？它们分别帮你自动 impl 了哪个 trait？

> 宏定义必须在使用之前，而且常常要 `#[macro_export]` 才能跨模块用。

**配合阅读**：[官方书·第19章 宏](https://rustwiki.org/zh-CN/book/ch19-06-macros.html)
