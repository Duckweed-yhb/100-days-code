# Day 65 · TCP服务端

## 今日目标
- 学会写一个 TCP 服务器
- 理解每连接一个 goroutine
- 处理连接的读写与关闭

## 学习内容

### TCP 服务端骨架

```go
package main

import (
    "fmt"
    "net"
)

func handleConn(conn net.Conn) {
    defer conn.Close()
    buf := make([]byte, 1024)
    for {
        n, err := conn.Read(buf)
        if err != nil {
            return
        }
        fmt.Println("收到:", string(buf[:n]))
        conn.Write([]byte("已收到: " + string(buf[:n])))
    }
}

func main() {
    listener, err := net.Listen("tcp", ":8080")
    if err != nil {
        panic(err)
    }
    defer listener.Close()
    fmt.Println("监听 :8080")

    for {
        conn, err := listener.Accept()
        if err != nil {
            continue
        }
        go handleConn(conn)     // 每连接一个 goroutine
    }
}
```

### 关键模式

- `Accept()` 阻塞等待新连接
- 每个连接开一个 goroutine 处理
- 这就是 Go 高并发服务器的雏形

### 测试

另开一个终端：

```powershell
# PowerShell 用 Test-NetConnection 或 telnet
# 简单点用 nc 或另写个客户端
```

明天写客户端。

### 注意

- Read 返回 err 表示连接断开，直接 return
- 要处理粘包问题（Day66）
- 生产环境要加超时控制（用 conn.SetDeadline）

## 核心重点
- Listen 监听，Accept 接连接
- 每连接一个 goroutine
- Read 返回 err 即断开
- defer conn.Close()
- Go 的并发模型让 TCP 服务器很简洁

## 练习/任务
1. 跑通 TCP 服务端
2. 用 telnet 或另写客户端连上去发消息
3. 让服务端把收到的消息原样回显
4. 加 SetDeadline 超时断开空闲连接

## 今日刷题

### 1. LeetCode 200 岛屿数量（复习）
- 思路：DFS。
- 代码提示：沉没岛屿。
- Go 要点：网格 DFS。

### 2. LeetCode 133 克隆图
- 思路：DFS/BFS + map 存旧节点→新节点。
- 代码提示：避免重复克隆。
- Go 要点：图遍历。

## 打卡清单
- [ ] 会写 TCP 服务端
- [ ] 理解每连接 goroutine
- [ ] 会处理连接断开
- [ ] 写完岛屿与克隆图
