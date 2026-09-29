# Day50 序列化 serde

> 目标：学会用 serde 把 Rust 结构体和 JSON 字符串互相转换，这是后续做网络接口、配置文件、数据处理的基础技能。

## 什么是序列化

**序列化（serialization）**就是把内存里的对象（结构体、枚举）转成可以存盘或网络传输的格式（比如 JSON、二进制）；**反序列化（deserialization）**就是反过来，把字符串/字节还原成对象。

生活比喻：序列化像把一个人拆成“姓名、年龄、地址”一张表格寄出去；反序列化像收到表格后照着它拼回一个人。

Rust 生态里做这件事的事实标准就是 **serde**。它本身不负责具体格式（JSON、CBOR、MessagePack……），而是定义一套接口；具体格式由 `serde_json`、`serde_cbor` 等 crate 实现。

## 添加依赖

`serde` 的核心 derive 功能在 `derive` feature 里，要显式打开；`serde_json` 负责 JSON：

```toml
# Cargo.toml
[dependencies]
serde = { version = "1.0", features = ["derive"] }
serde_json = "1.0"
```

> 这两个 crate 需要联网拉取，第一次 `cargo build` 会自动下载编译。

## 最基本的例子：结构体 ↔ JSON

```rust
use serde::{Serialize, Deserialize};

// 派生 Serialize 和 Deserialize，serde 就知道怎么读写这个结构体
#[derive(Debug, Serialize, Deserialize)]
struct User {
    name: String,
    age: u32,
}

fn main() -> Result<(), serde_json::Error> {
    // 1. 序列化：结构体 -> JSON 字符串
    let user = User { name: "Alice".into(), age: 30 };
    let json = serde_json::to_string(&user)?;
    println!("序列化: {}", json);
    // 输出: {"name":"Alice","age":30}

    // 美化输出（带缩进）
    let pretty = serde_json::to_string_pretty(&user)?;
    println!("美化版:\n{}", pretty);

    // 2. 反序列化：JSON 字符串 -> 结构体
    let s = r#"{"name": "Bob", "age": 25}"#;   // r#"..."# 是原始字符串，不用转义引号
    let user2: User = serde_json::from_str(s)?;
    println!("反序列化: {:?}", user2);

    Ok(())
}
```

记住两个核心函数：

| 函数 | 作用 |
|---|---|
| `serde_json::to_string(&value)` | 序列化成紧凑 JSON |
| `serde_json::to_string_pretty(&value)` | 序列化成带缩进的 JSON |
| `serde_json::from_str::<T>(s)` | 反序列化成类型 T |

## Option：可选字段

JSON 里经常有“字段可能不存在”的情况，用 `Option<T>` 表示：

```rust
use serde::{Serialize, Deserialize};

#[derive(Debug, Serialize, Deserialize)]
struct User {
    name: String,
    age: u32,
    #[serde(skip_serializing_if = "Option::is_none")]  // None 时不输出这个字段
    email: Option<String>,
}

fn main() -> Result<(), serde_json::Error> {
    let u = User {
        name: "Carol".into(),
        age: 28,
        email: None,   // 没有邮箱
    };
    // 因为 skip_serializing_if，输出里不会有 "email" 字段
    println!("{}", serde_json::to_string(&u)?);
    // {"name":"Carol","age":28}

    let u2 = User {
        name: "Dave".into(),
        age: 35,
        email: Some("dave@example.com".into()),
    };
    println!("{}", serde_json::to_string(&u2)?);
    // {"name":"Dave","age":35,"email":"dave@example.com"}
    Ok(())
}
```

反序列化时，如果 JSON 里缺了某个非 Option 字段，`from_str` 会报错；`Option` 字段缺失则会被填成 `None`。

## 嵌套结构和集合

结构体可以嵌套，Vec、HashMap 都能直接序列化：

```rust
use serde::{Serialize, Deserialize};

#[derive(Debug, Serialize, Deserialize)]
struct Address {
    city: String,
    street: String,
}

#[derive(Debug, Serialize, Deserialize)]
struct Company {
    name: String,
    address: Address,           // 嵌套结构体
    employees: Vec<User>,       // Vec
    tags: Vec<String>,
}

#[derive(Debug, Serialize, Deserialize)]
struct User {
    name: String,
    age: u32,
}

fn main() -> Result<(), serde_json::Error> {
    let company = Company {
        name: "火星科技".into(),
        address: Address {
            city: "深圳".into(),
            street: "科技园路 1 号".into(),
        },
        employees: vec![
            User { name: "Alice".into(), age: 30 },
            User { name: "Bob".into(), age: 25 },
        ],
        tags: vec!["rust".into(), "web".into()],
    };

    println!("{}", serde_json::to_string_pretty(&company)?);
    Ok(())
}
```

## json! 宏：动态构造 JSON

有时候你不想定义结构体，就想随手拼一个 JSON（比如测试时 mock 接口返回），用 `serde_json::json!` 宏：

```rust
use serde_json::json;

fn main() {
    let data = json!({
        "name": "测试公司",
        "founding_year": 2024,
        "active": true,
        "address": {
            "city": "深圳",
            "zip": null
        },
        "employees": ["Alice", "Bob"]
    });

    // data 的类型是 serde_json::Value，可以随意索引
    println!("公司名: {}", data["name"]);
    println!("城市: {}", data["address"]["city"]);

    // 转成字符串
    println!("{}", serde_json::to_string_pretty(&data).unwrap());
}
```

`json!` 宏产出的类型是 `serde_json::Value`——一个“动态 JSON 值”枚举，能表示任意 JSON 结构（对象、数组、字符串、数字、布尔、null）。当你不知道数据长什么样、或者结构变化时，用 `Value` 最灵活；知道结构时还是定义结构体更类型安全。

## 枚举的序列化

serde 默认把序列枚举成带标签的 JSON。最简单的无字段枚举：

```rust
use serde::{Serialize, Deserialize};

#[derive(Debug, Serialize, Deserialize)]
enum Color {
    Red,
    Green,
    Blue,
}

fn main() -> Result<(), serde_json::Error> {
    let c = Color::Red;
    println!("{}", serde_json::to_string(&c)?);   // "Red"
    Ok(())
}
```

## 实战：读写 JSON 配置文件

把今天的东西和 Day48 的文件 IO 合起来，就是一个最常见的场景——读写配置文件：

```rust
use serde::{Serialize, Deserialize};
use std::fs;

#[derive(Debug, Serialize, Deserialize)]
struct Config {
    host: String,
    port: u16,
    debug: bool,
}

fn main() -> Result<(), Box<dyn std::error::Error>> {
    // 写配置
    let cfg = Config { host: "127.0.0.1".into(), port: 7878, debug: true };
    fs::write("config.json", serde_json::to_string_pretty(&cfg)?)?;

    // 读配置
    let text = fs::read_to_string("config.json")?;
    let loaded: Config = serde_json::from_str(&text)?;
    println!("加载配置: {:?}", loaded);
    Ok(())
}
```

## 今日小结

- serde 是 Rust 序列化事实标准，`serde_json` 负责 JSON 格式
- 依赖：`serde = { version = "1.0", features = ["derive"] }` + `serde_json = "1.0"`
- 给结构体 derive `Serialize, Deserialize`，然后 `serde_json::to_string` / `from_str` 互转
- `Option<T>` 表示可选字段，配合 `#[serde(skip_serializing_if)]` 控制输出
- 结构未知时用 `serde_json::json!` 宏和 `Value` 类型动态操作
- 配 `std::fs` 就能读写 JSON 配置文件

## 动手任务

1. 定义一个 `Student { name: String, score: u32, email: Option<String> }`，派生 Serialize/Deserialize，把它序列化成 JSON 字符串再反序列化回来，断言字段一致。
2. 用 `json!` 宏构造一个包含 3 个学生的 JSON 数组，然后用 `from_str` 反序列化成 `Vec<Student>`。
3. 写一个程序：把上一题的 Vec 写入 `students.json`，再读回来打印。

> 提示：反序列化时编译器需要知道目标类型，可以写 `serde_json::from_str::<Vec<Student>>(&s)` 或让左侧变量类型标注推导。

**配合阅读**：[serde 官网](https://serde.rs/) · [serde_json 文档](https://docs.rs/serde_json)
