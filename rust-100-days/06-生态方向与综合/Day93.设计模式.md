# Day93 设计模式

> 目标：掌握 Rust 里真正常用的几个模式——newtype、builder、RAII guard、typestate、extension trait，学会把错误编码进类型系统。

## Rust 里的设计模式和 GoF 不一样

经典《设计模式》那本书是面向对象时代写的，很多模式（继承、子类多态）在 Rust 里根本不存在。Rust 社区总结了一套**适合所有权/类型系统**的模式，核心思路是：**把约束写进类型，让编译器帮你拦错**。

## newtype：用类型区分语义

`String` 就是 `String`，但「用户 ID」和「订单 ID」都是 `u64`，万一传混了？newtype 用结构体包一层：

```rust
struct UserId(u64);
struct OrderId(u64);

fn get_user(id: UserId) -> String { format!("用户 {}", id.0) }

// get_user(OrderId(123));  // 编译错误！类型不同，传不进去
```

零运行时开销（和直接存 u64 一样），但编译期就防止了张冠李戴。

## builder：构造复杂对象

一个对象有十几个可选参数，构造函数会变成一长串难记的参数。builder 模式链式设置：

```rust
struct Server {
    host: String,
    port: u16,
    threads: usize,
}

impl Server {
    fn builder() -> ServerBuilder {
        ServerBuilder::default()
    }
}

#[derive(Default)]
struct ServerBuilder {
    host: Option<String>,
    port: Option<u16>,
    threads: Option<usize>,
}

impl ServerBuilder {
    fn host(mut self, h: &str) -> Self { self.host = Some(h.into()); self }
    fn port(mut self, p: u16) -> Self { self.port = Some(p); self }
    fn build(self) -> Server {
        Server {
            host: self.host.unwrap_or_else(|| "127.0.0.1".into()),
            port: self.port.unwrap_or(3000),
            threads: self.threads.unwrap_or(4),
        }
    }
}

// 用的时候：谁必填谁选填一目了然
let srv = Server::builder().port(8080).build();
```

实际项目用 `derive_builder` crate 自动生成。

## RAII guard：离开作用域自动清理

这其实是 Rust 的默认能力。你 Day33 学的 `MutexGuard` 就是典型——加锁返回一个 guard，**出作用域自动 drop 解锁**。你不用手写 `unlock()`，忘记解锁都难。

数据库连接、文件锁、事务提交，都用这个模式：**资源获取即初始化，释放即 drop**。

## typestate：用类型状态机防止误用

这是 Rust 最妙的模式之一。把「对象当前处于什么状态」编码进类型，**非法操作编译期就不让你做**。

比如一个构建请求的流程：未验证 → 已验证 → 已发送。你不可能在「未验证」状态就发送——因为类型根本不对：

```rust
struct Draft;      // 草稿态
struct Verified;   // 已验证态

struct Request<State> {
    body: String,
    _state: std::marker::PhantomData<State>,
}

impl Request<Draft> {
    fn verify(self) -> Request<Verified> {  // 只有 Draft 能调用
        Request { body: self.body, _state: std::marker::PhantomData }
    }
}

impl Request<Verified> {
    fn send(self) {  // 只有 Verified 能 send
        println!("发送：{}", self.body);
    }
}
```

`send` 只在 `Request<Verified>` 上存在。`Request<Draft>` 想 send？编译错误。这就是「用类型写状态机」。

## extension trait：给已有类型加方法

不能给别人 crate 的类型 impl 方法（孤儿规则）？用 trait 包一下：

```rust
trait MyStringExt {
    fn shout(&self) -> String;
}

impl MyStringExt for String {
    fn shout(&self) -> String {
        self.to_uppercase() + "!"
    }
}

// 现在 String 就多了个 shout 方法
let s = "hello".to_string();
println!("{}", s.shout());
```

## 把错误编码进类型系统

传统语言用运行时异常，Rust 倾向于**用类型表达可能性**：

- 空不存在 → `Option<T>`
- 失败不 panic → `Result<T, E>`
- 状态不合法 → typestate 让它根本编不过

这是 Rust 设计哲学的核心：**能在编译期解决的，别留到运行时**。

## 今日小结

- newtype 用结构体包一层，零成本防止语义混淆
- builder 链式构造复杂对象，必填选填一目了然
- RAII guard：drop 自动解锁/释放，忘记都难
- typestate 把状态编进类型，非法操作编译期报错
- extension trait 绕过孤儿规则给已有类型加方法
- 核心思想：用类型系统在编译期拦错

## 动手任务

1. 给你项目里某个「到处传的 ID」改成 newtype
2. 给一个多参数结构体写一个 builder（可以用 derive_builder）
3. 想一个你的业务里「有状态流转」的对象，用 typestate 写出来

**配合阅读**：[Rust Design Patterns](https://rust-unofficial.github.io/patterns/)
