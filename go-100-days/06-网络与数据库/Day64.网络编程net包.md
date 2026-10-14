# Day 64 · 网络编程net包

## 今日目标
- 理解 TCP/IP 基本概念
- 学会 net 包的基本拨号与监听
- 区分 TCP 与 UDP

## 学习内容

### 网络分层速览

应用层（HTTP）→ 传输层（TCP/UDP）→ 网络层（IP）→ 链路层。Go 的 `net` 包封装了传输层以上的细节。

### TCP vs UDP

| 特性 | TCP | UDP |
|---|---|---|
| 连接 | 面向连接 | 无连接 |
| 可靠 | 保证送达、有序 | 尽力而为 |
| 速度 | 较慢 | 快 |
| 场景 | HTTP、数据库 | 视频、DNS |

### net.Dial

主动连出去：

```go
conn, err := net.Dial("tcp", "example.com:80")
if err != nil {
    log.Fatal(err)
}
defer conn.Close()
```

`conn` 是一个连接，实现了 Reader/Writer，可以像操作文件一样读写。

### 读写连接

```go
conn.Write([]byte("GET / HTTP/1.0\r\n\r\n"))
buf := make([]byte, 1024)
n, _ := conn.Read(buf)
fmt.Println(string(buf[:n]))
```

### net.Listen

作为服务端监听端口（明天详写）：

```go
listener, err := net.Listen("tcp", ":8080")
```

## 核心重点
- net 包封装了 TCP/UDP
- Dial 主动连，Listen 被动等
- conn 实现 Reader/Writer，可读写
- TCP 可靠面向连接，UDP 快无连接
- 连接用完要 Close

## 练习/任务
1. 用 net.Dial 连到 example.com:80 发一个 HTTP 请求
2. 读回响应并打印前几百字节
3. 用 net.Listen 监听一个端口（明天再写处理）
4. 对比 TCP 与 UDP 适用场景

## 今日刷题

### 1. LeetCode 704 二分查找（复习）
- 思路：双指针。
- 代码提示：mid = lo+(hi-lo)/2。
- Go 要点：二分。

### 2. LeetCode 35 搜索插入位置（复习）
- 思路：二分找边界。
- 代码提示：第一个 >= target。
- Go 要点：二分。

## 打卡清单
- [ ] 会用 net.Dial 拨号
- [ ] 会读写 conn
- [ ] 理解 TCP/UDP 区别
- [ ] 复习二分查找
