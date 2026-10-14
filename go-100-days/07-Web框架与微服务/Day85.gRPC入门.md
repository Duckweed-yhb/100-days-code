# Day 85 · gRPC入门

## 今日目标
- 理解 gRPC 与 REST 的区别
- 学会用 protoc 生成 Go 代码
- 写一个最简 gRPC 服务

## 学习内容

### gRPC 是什么

Google 开源的 RPC 框架，用 HTTP/2 传输、Protobuf 序列化，跨语言、高性能。

### 对比 REST

| 特性 | REST | gRPC |
|---|---|---|
| 协议 | HTTP/1.1 | HTTP/2 |
| 序列化 | JSON | Protobuf |
| 流式 | 不支持 | 支持双向流 |
| 代码生成 | 手动 | 自动 |

### 安装工具

```powershell
go install google.golang.org/protobuf/cmd/protoc-gen-go@latest
go install google.golang.org/grpc/cmd/protoc-gen-go-grpc@latest
```

### 定义 proto

```proto
syntax = "proto3";
package hello;

service Greeter {
    rpc SayHello (HelloRequest) returns (HelloReply) {}
}

message HelloRequest {
    string name = 1;
}
message HelloReply {
    string message = 1;
}
```

### 生成代码

```powershell
protoc --go_out=. --go-grpc_out=. hello.proto
```

### 写服务端

```go
type server struct {
    pb.UnimplementedGreeterServer
}

func (s *server) SayHello(ctx context.Context, req *pb.HelloRequest) (*pb.HelloReply, error) {
    return &pb.HelloReply{Message: "你好, " + req.Name}, nil
}
```

## 核心重点
- gRPC 用 Protobuf + HTTP/2
- 先写 .proto 定义服务与消息
- protoc 生成 Go 代码
- 服务端实现接口，客户端 stub 调用
- 高性能跨语言 RPC

## 练习/任务
1. 安装 protoc 与 Go 插件
2. 定义一个 Greeter proto 并生成代码
3. 跑通服务端与客户端
4. 加一个 SayGoodbye 方法

## 今日刷题

### 1. LeetCode 20 有效的括号（复习）
- 思路：栈。
- 代码提示：map。
- Go 要点：栈。

### 2. LeetCode 21 合并两个有序链表（复习）
- 思路：哑节点。
- 代码提示：谁小接谁。
- Go 要点：链表。

## 打卡清单
- [ ] 理解 gRPC 概念
- [ ] 会写 proto 文件
- [ ] 会生成 Go 代码
- [ ] 复习括号与链表
