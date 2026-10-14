# Day 66 · TCP客户端与粘包

## 今日目标
- 学会写 TCP 客户端
- 理解粘包成因与解法
- 掌握简单的长度前缀协议

## 学习内容

### TCP 客户端

```go
conn, err := net.Dial("tcp", "127.0.0.1:8080")
if err != nil {
    log.Fatal(err)
}
defer conn.Close()

conn.Write([]byte("hello server"))
buf := make([]byte, 1024)
n, _ := conn.Read(buf)
fmt.Println(string(buf[:n]))
```

### 什么是粘包

TCP 是字节流，没有消息边界。客户端连发两条消息，服务端可能一次 Read 读到两条粘在一起：

```
客户端发: "AB" 和 "CD"
服务端一次 Read: "ABCD" 或 "AB" 或 "A" + "BCD"
```

### 解法：自定义协议

常用"长度前缀"：先写 4 字节长度，再写消息体。

```go
import "encoding/binary"

// 发送方
func sendMessage(conn net.Conn, msg []byte) error {
    lenBuf := make([]byte, 4)
    binary.BigEndian.PutUint32(lenBuf, uint32(len(msg)))
    if _, err := conn.Write(lenBuf); err != nil {
        return err
    }
    _, err := conn.Write(msg)
    return err
}

// 接收方
func readMessage(conn net.Conn) ([]byte, error) {
    lenBuf := make([]byte, 4)
    if _, err := io.ReadFull(conn, lenBuf); err != nil {
        return nil, err
    }
    n := binary.BigEndian.Uint32(lenBuf)
    msg := make([]byte, n)
    _, err := io.ReadFull(conn, msg)
    return msg, err
}
```

### io.ReadFull

`io.ReadFull` 一直读到 buf 满为止，避免读半包。

## 核心重点
- TCP 是字节流，没有消息边界
- 粘包要应用层自己定义协议
- 长度前缀协议：4 字节大端长度 + 消息体
- io.ReadFull 保证读满
- 真实项目用 protobuf 等成熟协议

## 练习/任务
1. 写一个 TCP 客户端连到 Day65 的服务端
2. 实现长度前缀协议的发送与接收
3. 连发 5 条消息，验证服务端能正确分包
4. 故意一次 Read 小缓冲，体会粘包

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
- [ ] 会写 TCP 客户端
- [ ] 理解粘包成因
- [ ] 会实现长度前缀协议
- [ ] 复习链表题
