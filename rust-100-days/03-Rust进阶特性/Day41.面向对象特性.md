# Day41 面向对象特性

> 目标：看清 Rust 怎么对应面向对象（OOP）的封装与多态——它没有传统继承，但用 trait 和 trait 对象把这两件事办得更漂亮，并动手实现一个状态模式。

## Rust 算面向对象吗

不同语言对「对象」定义不一样。Rust 受多范式影响：它有把数据和行为绑在一起的结构体（`struct` + `impl`），有封装，但**没有传统意义上的类继承**。我们逐个看 OOP 三大特征在 Rust 里怎么对应：

| OOP 特点 | Rust 怎么做 |
|---|---|
| 对象 = 数据 + 行为 | 结构体/枚举存数据，`impl` 块写方法 |
| 封装（隐藏内部细节） | `pub` 控制公有/私有，字段默认私有 |
| 继承（重用 + 子类型多态） | **不用**；重用靠 trait 默认方法，多态靠 trait 对象 |

## 封装：pub 与私有

结构体字段默认就是私有的，外部摸不到。你只暴露必要的公有方法，内部想怎么改都行：

```rust
pub struct AveragedCollection {
    list: Vec<i32>,   // 私有，外部不能直接 push
    average: f64,     // 私有，外部不能直接改
}

impl AveragedCollection {
    pub fn add(&mut self, value: i32) {
        self.list.push(value);
        self.update_average(); // 改 list 的同时自动重算 average，保证一致
    }

    pub fn average(&self) -> f64 {
        self.average // 只读暴露
    }

    fn update_average(&mut self) { // 私有方法，外部调不到
        let total: i32 = self.list.iter().sum();
        self.average = total as f64 / self.list.len() as f64;
    }
}
```

外部只能 `add` 和读 `average`，永远不会拿到一个「list 改了但 average 没更新」的中间状态。这就是封装的价值。

## 没有继承，那代码怎么复用

传统继承干两件事：复用代码、多态。Rust 分别替代：

- **复用代码** → trait 的**默认方法实现**：实现了这个 trait 就白送一个方法，也可以自己覆盖。
- **子类型多态** → **trait 对象**（下面讲）。

## trait 对象：动态多态

假设你要做一个 GUI，屏幕上能放按钮、下拉框……它们都能「画自己」，但是不同类型。你想在一个 `Vec` 里把它们混着装起来，挨个调 `draw()`。

泛型 + trait bound 做不到混装（它要求整个 Vec 是同一种类型）。这时候用 **trait 对象**：`Box<dyn Draw>`。

```rust
pub trait Draw {
    fn draw(&self);
}

pub struct Screen {
    // 一个 vec 里可以装任何实现了 Draw 的不同类型
    pub components: Vec<Box<dyn Draw>>,
}

impl Screen {
    pub fn run(&self) {
        for c in &self.components {
            c.draw(); // 运行时才知道调的是哪个类型的 draw
        }
    }
}

pub struct Button { pub label: String }
impl Draw for Button {
    fn draw(&self) { println!("画一个按钮: {}", self.label); }
}

pub struct SelectBox { pub options: Vec<String> }
impl Draw for SelectBox {
    fn draw(&self) { println!("画下拉框: {:?}", self.options); }
}

fn main() {
    let screen = Screen {
        components: vec![
            Box::new(SelectBox { options: vec!["是".into(), "否".into()] }),
            Box::new(Button { label: "确定".into() }),
        ],
    };
    screen.run();
}
```

运行上面命令看看输出：按钮和下拉框的 `draw` 都被调到了。

### 静态分发 vs 动态分发

| | 泛型 + `T: Trait` | `Box<dyn Trait>` |
|---|---|---|
| 分发 | 静态分发（编译期单态化） | 动态分发（运行时查表） |
| 集合元素 | 必须全是同一种类型 | 可混装多种实现 |
| 性能 | 可内联，更快 | 有间接调用开销 |
| 灵活性 | 低 | 高 |

> **对象安全**：只有方法不返回 `Self`、没有泛型参数的 trait 才能当 trait 对象。比如 `Clone` 因为 `clone()` 返回 `Self`，就不能做成 `Box<dyn Clone>`。

## 状态模式

状态模式：一个对象内部有好几个状态，行为随状态变化，外部只调统一方法，状态自己决定怎么转。经典例子是博文工作流：草稿 `Draft` → 审核中 `PendingReview` → 已发布 `Published`。

```rust
pub struct Post {
    state: Option<Box<dyn State>>, // 当前状态是个 trait 对象
    content: String,
}

trait State {
    fn request_review(self: Box<Self>) -> Box<dyn State>;
    fn approve(self: Box<Self>) -> Box<dyn State>;
    fn content<'a>(&self, _post: &'a Post) -> &'a str { "" } // 默认拿不到内容
}

struct Draft {}
impl State for Draft {
    fn request_review(self: Box<Self>) -> Box<dyn State> { Box::new(PendingReview {}) }
    fn approve(self: Box<Self>) -> Box<dyn State> { self } // 草稿审核无效
}

struct PendingReview {}
impl State for PendingReview {
    fn request_review(self: Box<Self>) -> Box<dyn State> { self }
    fn approve(self: Box<Self>) -> Box<dyn State> { Box::new(Published {}) }
}

struct Published {}
impl State for Published {
    fn request_review(self: Box<Self>) -> Box<dyn State> { self }
    fn approve(self: Box<Self>) -> Box<dyn State> { self }
    fn content<'a>(&self, post: &'a Post) -> &'a str { &post.content } // 发布后才能读
}

impl Post {
    pub fn new() -> Post {
        Post { state: Some(Box::new(Draft {})), content: String::new() }
    }
    pub fn add_text(&mut self, s: &str) { self.content.push_str(s); }
    pub fn request_review(&mut self) {
        if let Some(s) = self.state.take() { self.state = Some(s.request_review()); }
    }
    pub fn approve(&mut self) {
        if let Some(s) = self.state.take() { self.state = Some(s.approve()); }
    }
    pub fn content(&self) -> &str { self.state.as_ref().unwrap().content(self) }
}

fn main() {
    let mut post = Post::new();
    post.add_text("我今天吃了沙拉");
    assert_eq!("", post.content()); // 草稿阶段拿不到内容
    post.request_review();
    assert_eq!("", post.content()); // 审核中也拿不到
    post.approve();
    assert_eq!("我今天吃了沙拉", post.content()); // 发布后才能读
}
```

> 更 Rust 风格的替代方案：把每个状态做成**不同的类型**——`DraftPost` 才有 `request_review()`，返回 `PendingReviewPost`，再 `approve()` 才得到能读 `content()` 的 `Post`。这样「草稿阶段就读内容」这种非法操作在编译期就报错，比运行时状态机更安全。

## 今日小结

- Rust 有封装（字段默认私有、只暴露 pub 方法），但没有传统类继承
- 代码复用靠 trait 默认方法；多态靠 `Box<dyn Trait>` trait 对象
- 泛型是静态分发（快、同类型），`dyn Trait` 是动态分发（灵活、可混装）
- 状态模式用 `Option<Box<dyn State>>`，各状态自行决定如何转移
- 更地道的 Rust 做法：把状态编码进类型，让非法状态编译期报错

## 动手任务

1. 给 `Screen` 再加一个组件类型（比如 `Checkbox`），实现 `Draw`，确认能和按钮、下拉框混装。
2. 给 `Post` 加一个「拒稿」动作：审核中调用后回到 `Draft`。
3. 思考：为什么 `Clone` 不能做成 `Box<dyn Clone>`？（提示：方法返回 `Self`）

> `dyn` 背后要堆分配，trait 对象都得包在 `Box` 里，别忘了 `Box::new(...)`。

**配合阅读**：[官方书·第17章 trait 对象](https://rustwiki.org/zh-CN/book/ch17-02-trait-objects.html)
