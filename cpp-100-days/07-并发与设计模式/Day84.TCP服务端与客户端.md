# Day 84 · TCP服务端与客户端

## 今日目标
- 理解一个 TCP 连接的完整流程
- 了解阻塞与 IO 多路复用
- 知道简单 HTTP 服务长什么样

## 学习内容

### 一次 TCP 连接的流程

1. 服务器 `socket()` → `bind()` 到端口 → `listen()`；
2. 客户端 `socket()` → `connect()`；
3. 服务器 `accept()` 返回新连接；
4. 双方 `read()` / `write()` 收发数据；
5. 关闭连接。

### 阻塞模型

最简单的服务端：每来一个连接 `accept()`，然后 `read()`，没数据就卡住。这种模型一次只能服务一个客户端。

### IO 多路复用

用 `select` / `poll` / `epoll`（Linux）一个线程同时监听多个连接：

- 哪个连接有数据了就处理哪个；
- 这是 nginx、redis 等高性能服务器的基础。

### 简单 HTTP 服务概念

HTTP 就是"客户端发一段文本请求，服务端回一段文本响应"：

```
GET / HTTP/1.1
Host: example.com

```

服务端回：

```
HTTP/1.1 200 OK
Content-Length: 11

Hello world
```

### 多线程服务端

每个连接开一个线程：

```
main: accept
  -> 来一个连接，开 thread 处理
```

简单但连接多了线程爆炸。

### 本阶段到此为止

我们不要求写能跑的 socket 代码（Windows/Linux API 差异大），理解模型即可。做后端开发再深入。

## 核心重点
- TCP 流程：socket/bind/listen/accept/read/write/close
- 阻塞模型一次服务一个客户端
- IO 多路复用是高性能服务器基础
- HTTP 是文本协议
- 今天理解概念即可

## 练习/任务
1. 用 curl 访问一个网站，观察响应头
2. 画一下 TCP 连接的时序图
3. （选做）查资料了解 epoll 是什么
4. （选做）了解为什么多线程服务器不能无限开线程

## 今日刷题

- **LeetCode 200. 岛屿数量**。
- **LeetCode 133. 克隆图**。
- **LeetCode 127. 单词接龙**。

## 打卡清单
- [ ] 已理解 TCP 连接流程
- [ ] 已知道阻塞与多路复用
- [ ] 已认识 HTTP 文本格式
- [ ] 已了解多线程服务器模型
