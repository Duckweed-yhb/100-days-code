# Day 74 · netRpc远程调用

## 今日目标
- 理解 RPC 思想
- 学会用标准库 net/rpc 做远程调用
- 认识 RPC 与 HTTP 的区别

## 学习内容

### RPC 是什么

RPC（远程过程调用）像调用本地函数一样调用另一台机器上的函数，底层通信细节被屏蔽。

### 定义服务

```go
package main

import (
    "net"
    "net/rpc"
)

type Args struct {
    A, B int
}

type Calculator struct{}

func (c *Calculator) Add(args Args, reply *int) error {
    *reply = args.A + args.B
    return nil
}

func main() {
    calc := new(Calculator)
    rpc.Register(calc)

    listener, _ := net.Listen("tcp", ":1234")
    for {
        conn, _ := listener.Accept()
        go rpc.ServeConn(conn)
    }
}
```

方法必须满足：导出、两个参数、第二个是指针、返回 error。

### 客户端

```go
client, err := rpc.Dial("tcp", "localhost:1234")
var result int
err = client.Call("Calculator.Add", Args{3, 4}, &result)
fmt.Println(result)   // 7
```

### 限制

标准 net/rpc 用 Gob 编码，只能 Go 调 Go。跨语言场景用 gRPC（Day85 学）。

## 核心重点
- RPC 像调本地函数一样调远程
- net/rpc 是标准库入门
- 方法签名有严格要求
- 客户端 client.Call 调用
- 跨语言用 gRPC

## 练习/任务
1. 跑通 Calculator RPC
2. 加一个 Multiply 方法
3. 写客户端并发调用
4. 故意写错方法名，观察错误

## 今日刷题

### 1. LeetCode 206 反转链表（复习）
- 思路：三指针。
- 代码提示：prev/cur/next。
- Go 要点：链表。

### 2. LeetCode 141 环形链表（复习）
- 思路：快慢指针。
- 代码提示：相遇。
- Go 要点：链表。

## 打卡清单
- [ ] 理解 RPC 概念
- [ ] 会写 net/rpc 服务端
- [ ] 会写 RPC 客户端
- [ ] 复习链表题
