# Day81 Web 后端

> 目标：搞清楚 Rust 写 Web 后端现在用什么框架，能跑起一个 axum 小服务，并会在 axum 和 actix-web 之间做选型。

## 为什么 Rust 也能写后端

你已经用 `std::net` 手搓过多线程 Web 服务器（Day54~55）。真要上生产，不能自己处理路由、JSON、中间件、连接池这些脏活。Rust 后端生态这几年已经很成熟，核心卖点是：**和 Go 差不多的并发能力，但内存更小、吞吐更高，还不用 GC 停顿**。

现在 Rust 后端基本都跑在 **tokio** 这个异步运行时上。你在 Day40 学的 `async/await`，到了 Web 框架里就是日常。

## 两大主流框架：axum vs actix-web

| 维度 | axum | actix-web |
|---|---|---|
| 出身 | tokio 官方团队（tokio-rs） | Actix 社区，最早主打 actor 模型 |
| 底层 | 基于 hyper + tower | 自己一套异步运行时抽象 |
| 路由 | 函数式签名路由，无过程宏 | 早期靠 `#[get]` 路由宏 |
| 中间件 | 不另造轮子，直接复用 tower / tower-http | 自有中间件机制 |
| 风格 | 提取器（extractor）把请求拆成参数 | 同样支持提取器 |
| 适合谁 | 想用 tokio 生态、和 tonic/gRPC 打通的团队 | 追求极致性能、历史项目 |

一句话：**现在新项目默认选 axum**，它是 tokio 官方亲儿子，和中间件生态（日志、压缩、CORS、追踪）无缝衔接。actix-web 依然很快、很稳，但社区新项目越来越多流向 axum。

### axum 的三个核心概念

- **Router**：把路径映射到处理函数，像一张「地址簿」
- **Handler（处理函数）**：一个普通 `async fn`，参数就是提取器
- **Extractor（提取器）**：从请求里自动取出你想要的东西——路径参数、查询串、JSON Body、请求头，类型对不上自动返回 400

## 最小 axum 服务

先加依赖（`Cargo.toml`）：

```toml
[dependencies]
axum = "0.8"              # Web 框架
tokio = { version = "1", features = ["full"] }  # 异步运行时
tower-http = { version = "0.6", features = ["cors", "trace"] }  # 现成中间件
serde = { version = "1", features = ["derive"] }
```

```rust
use axum::{
    extract::{Path, Query},
    routing::{get, post},
    Json, Router,
};
use serde::Deserialize;
use std::collections::HashMap;

// 1) 路径参数：/hello/张三
async fn hello(Path(name): Path<String>) -> String {
    format!("你好，{}！", name)
}

// 2) 查询串提取：/greet?name=张三&age=18
#[derive(Deserialize)]
struct GreetParams {
    name: String,
    age: Option<u32>,
}

async fn greet(Query(p): Query<GreetParams>) -> String {
    match p.age {
        Some(age) => format!("{} 今年 {} 岁", p.name, age),
        None => format!("{} 你好", p.name),
    }
}

// 3) JSON 请求体 + 响应
#[derive(serde::Deserialize)]
struct CreateUser {
    name: String,
}

#[derive(serde::Serialize)]
struct User {
    id: u64,
    name: String,
}

async fn create_user(Json(payload): Json<CreateUser>) -> Json<User> {
    // 实际项目里这里会写数据库
    Json(User { id: 1, name: payload.name })
}

#[tokio::main]
async fn main() {
    // 组装路由
    let app = Router::new()
        .route("/hello/{name}", get(hello))
        .route("/greet", get(greet))
        .route("/users", post(create_user));

    // 绑定并启动
    let listener = tokio::net::TcpListener::bind("127.0.0.1:3000")
        .await
        .unwrap();
    println!("服务跑在 http://127.0.0.1:3000");
    axum::serve(listener, app).await.unwrap();
}
```

跑起来后用浏览器或 curl 试试：

```powershell
curl http://127.0.0.1:3000/hello/浮萍
curl "http://127.0.0.1:3000/greet?name=浮萍&age=20"
```

## 提取器是怎么省事的

注意上面 `hello` 的参数是 `Path(name): Path<String>`。这行的意思是：**axum 帮你把 URL 里 `{name}` 那段抠出来，转成 `String` 传给函数**。如果 URL 没带这段，axum 直接回 400，不用你手写判断。

同理，`Json<CreateUser>` 会自动把请求体解析成结构体，解析失败自动 400。这就是 Rust 后端「类型安全 + 少写样板」的爽点：**把 HTTP 协议的脏活全交给框架，你只写业务逻辑**。

## 中间件：复用 tower 生态

axum 自己不发明中间件，而是直接用 `tower`。日志、压缩、CORS、超时、追踪这些能力，`tower-http` 都写好了：

```rust
use tower_http::cors::CorsLayer;
use tower_http::trace::TraceLayer;

let app = Router::new()
    .route("/hello/{name}", get(hello))
    // 一行挂上日志和跨域中间件
    .layer(TraceLayer::new_for_http())
    .layer(CorsLayer::permissive());
```

以后你写 gRPC 用的 tonic、写客户端用的 hyper，都是同一个 tower 生态，中间件可以通用。

## actix-web 长什么样（了解即可）

```rust
use actix_web::{get, web, App, HttpResponse, HttpServer};

#[get("/{name}")]  // 早期用宏声明路由
async fn hello(name: web::Path<String>) -> HttpResponse {
    HttpResponse::Ok().body(format!("你好，{}", name.into_inner()))
}

#[actix_web::main]
async fn main() -> std::io::Result<()> {
    HttpServer::new(|| App::new().service(hello))
        .bind(("127.0.0.1", 3000))?
        .run()
        .await
}
```

它的宏路由看起来更直观，但中间件生态不如 tower 统一。选型时记住：**新项目 axum，维护老项目再看 actix-web**。

## 今日小结

- Rust 后端默认跑在 tokio 上，异步是日常
- axum = tokio 官方 + hyper/tower，函数式路由 + 提取器，新项目首选
- actix-web 性能强、有宏路由，但生态统一度不如 axum
- 提取器（Path/Query/Json）自动做参数解析和错误响应，少写样板
- 中间件直接复用 tower-http，和 tonic/hyper 打通

## 动手任务

1. 把上面的 axum 例子跑起来，用 curl 访问三个路由
2. 给 `create_user` 加一个内存 `HashMap<u64, User>` 存用户，实现一个 `GET /users/{id}` 查询接口
3. 挂上 `TraceLayer`，观察终端里打印的请求日志长什么样

**配合阅读**：[axum 官方文档](https://docs.rs/axum/latest/axum/)
