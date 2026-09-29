# Day98 综合大项目 2

> 目标：沿 Day97 的设计，按「先骨架后填肉」的顺序把核心代码写出来，知道每一步怎么验证、卡住了怎么排查。

## 推荐技术栈（以短链服务为例）

```toml
[dependencies]
axum = "0.8"                       # Web 框架
tokio = { version = "1", features = ["full"] }
sqlx = { version = "0.8", features = ["runtime-tokio-rustls", "sqlite"] }
serde = { version = "1", features = ["derive"] }
anyhow = "1"
tracing = "0.1"
tracing-subscriber = "0.3"
```

## 实现顺序：先骨架，后填肉

别一上来就写所有逻辑。按这个顺序，每步都能跑：

### 第 1 步：先把空服务跑起来

```rust
use axum::{routing::get, Router};

#[tokio::main]
async fn main() {
    let app = Router::new().route("/health", get(|| async { "ok" }));
    let listener = tokio::net::TcpListener::bind("127.0.0.1:3000").await.unwrap();
    println!("跑在 http://127.0.0.1:3000");
    axum::serve(listener, app).await.unwrap();
}
```

**验证**：`curl http://127.0.0.1:3000/health` 返回 ok。这步通了，再往下。

### 第 2 步：定义错误类型和数据结构

```rust
// error.rs：统一错误，实现 IntoResponse 让 axum 能转成 HTTP 响应
#[derive(Debug)]
enum AppError {
    NotFound,
    DbError(sqlx::Error),
}

impl IntoResponse for AppError {
    fn into_response(self) -> Response {
        (StatusCode::INTERNAL_SERVER_ERROR, "内部错误").into_response()
    }
}
```

### 第 3 步：写 store 层（先内存实现，再接数据库）

先用内存 map 跑通业务，**数据库最后换**——这就是面向 trait 编程的好处：

```rust
// store.rs
use std::collections::HashMap;
use std::sync::Mutex;

struct MemoryStore {
    map: Mutex<HashMap<String, String>>,
}

impl MemoryStore {
    async fn lookup(&self, code: &str) -> Option<String> {
        self.map.lock().unwrap().get(code).cloned()
    }
}
```

**验证**：写个单元测试存一个、取一个，过了再往下。

### 第 4 步：接路由，把 store 接进 handler

```rust
// POST /shorten：返回短码
async fn shorten(
    State(store): State<Arc<dyn Store>>,   // 共享存储
    Json(payload): Json<ShortenReq>,
) -> Json<ShortenResp> {
    let code = store.save(&payload.url).await.unwrap();
    Json(ShortenResp { code })
}
```

**验证**：curl POST 一个长 URL，看返回短码。

### 第 5 步：换真数据库

业务跑通后，把 `MemoryStore` 换成 `SqliteStore`，实现同一个 `Store` trait。handler 一行不改——这就是 Day93 说的「面向接口」的回报。

## 卡住了怎么排查

- **编译错误**：先读 rustc 的完整提示，它经常告诉你怎么改
- **运行 panic**：用 `RUST_BACKTRACE=1` 跑，看调用栈
- **HTTP 不通**：先 curl 直接打，排除前端问题；再看路由路径对不对
- **数据库错**：把 SQL 单独拿去 sqlite 命令行跑一遍

## 今日小结

- 按「先骨架后填肉」：空服务 → 错误类型 → store → 路由 → 数据库
- 每一步都能独立跑、独立验证，别攒一大堆再编译
- 先用内存实现跑通业务，最后换真数据库（面向 trait 的回报）
- 卡住先读编译器提示，用 backtrace 定位 panic
- 小步前进，每步可测

## 动手任务

1. 把空服务 `/health` 跑起来
2. 完成内存版 store，写单元测试存/取一条短链
3. 接上 POST /shorten 和 GET /{code}，用 curl 完整跑通一轮

**配合阅读**：[sqlx SQLite 示例](https://docs.rs/sqlx/latest/sqlx/)
