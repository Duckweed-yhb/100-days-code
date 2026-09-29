# Day90 分布式基础

> 目标：认识 Rust 写分布式系统的核心工具——tonic（gRPC）、tower（服务抽象）、tracing（追踪），并搞懂 Raft 一致性到底在解决什么问题。

## 分布式系统要解决什么

单机程序挂了就全挂。分布式是把服务拆成多台机器协作，带来三个新麻烦：

1. **服务间怎么通信** → RPC / gRPC
2. **一个请求跨了好几个服务，怎么排查** → 分布式追踪
3. **多台机器数据不一致怎么办** → 一致性算法（Raft）

Rust 因为性能好、内存安全，在云原生基础设施（数据库、中间件）里特别受欢迎。

## tonic：Rust 版 gRPC

**gRPC** 是一种 RPC 框架：你写一个 `.proto` 文件定义服务接口，工具自动生成客户端和服务端代码，跨语言调用。**tonic** 是 Rust 的 gRPC 实现，基于 tokio/hyper/tower，异步一等公民。

```protobuf
// hello.proto：先定义接口
// service Greeter {
//   rpc SayHello (HelloRequest) returns (HelloReply);
// }
// message HelloRequest { string name = 1; }
// message HelloReply   { string message = 1; }
```

```rust
// tonic 会基于 .proto 生成 Greeter 的 trait，你只要实现它
// #[tonic::async_trait]
// impl greeter_server::Greeter for MyService {
//     async fn say_hello(&self, req: Request<HelloRequest>)
//         -> Result<Response<HelloReply>, Status> {
//         Ok(Response::new(HelloReply {
//             message: format!("你好 {}", req.into_inner().name),
//         }))
//     }
// }
```

gRPC 跑在 HTTP/2 上，支持双向流式传输，微服务间通信首选。

## tower：服务抽象与中间件

你在 Day81 已经见过 tower——axum 的中间件就靠它。tower 的核心是一个 `Service` trait：「收到请求，返回响应」。所有超时、重试、限流、鉴权中间件都包装在 Service 外面。tonic、hyper、axum 共享同一套中间件生态。

## tracing：结构化日志与分布式追踪

线上一个请求经过 A→B→C 三个服务，出问题怎么定位？**tracing** 是 Rust 生态的结构化日志/追踪库，用「span（跨度）」把一次调用的全链路串起来：

```rust
use tracing::{info, instrument};

#[instrument]              // 这个函数自动成为一个 span
fn handle_request(user_id: u64) {
    info!("处理请求");
    do_db_query(user_id);
}

#[instrument]
fn do_db_query(user_id: u64) {
    info!(user_id, "查数据库");
}
```

配合 OpenTelemetry，可以把跨服务的 trace 串起来，在 Jaeger 这类工具里画出调用链。

## 一致性：Raft 是什么

多台机器存同一份数据，怎么保证大家不打架？**Raft** 是最容易理解的一致性算法：

- 节点分**领导者（Leader）**和**追随者（Follower）**
- 所有写请求都交给 Leader，Leader 复制给多数派 Follower 后才算提交
- Leader 挂了，剩下的节点重新选举一个新 Leader

这样即使少数节点挂了，系统依然可用且数据一致。Rust 里的实现有 **openraft**、**raft-rs**。你写分布式 KV 存储、配置中心时会用到。

## 今日小结

- tonic 是 Rust 的 gRPC 实现，基于 tokio/hyper/tower，跨语言服务通信
- tower 是 Service 抽象，统一了 axum/tonic/hyper 的中间件
- tracing 做结构化日志和分布式追踪，span 串起调用链
- Raft 解决多节点一致性：Leader 写、多数派确认、挂了重新选举
- openraft / raft-rs 是 Rust 的 Raft 实现

## 动手任务

1. 跑通 tonic 官方的 helloworld 示例，理解 .proto 怎么生成代码
2. 给一个函数加 `#[instrument]`，用 tracing-subscriber 观察 span 嵌套
3. 读一篇 Raft 动画讲解（搜 "Raft 动画"），搞懂选举和日志复制

**配合阅读**：[tonic 文档](https://docs.rs/tonic/latest/tonic/)
