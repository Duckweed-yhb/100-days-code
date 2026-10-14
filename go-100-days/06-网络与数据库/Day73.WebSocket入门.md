# Day 73 · WebSocket入门

## 今日目标
- 理解 WebSocket 与 HTTP 的区别
- 学会用 gorilla/websocket 做双向通信
- 写一个简单聊天室

## 学习内容

### WebSocket 是什么

HTTP 是请求-响应、一次性；WebSocket 在一次 HTTP 握手后升级为**全双工长连接**，服务端可以主动推消息给客户端。

### 安装 gorilla/websocket

```powershell
go get github.com/gorilla/websocket
```

### 服务端

```go
var upgrader = websocket.Upgrader{
    CheckOrigin: func(r *http.Request) bool { return true },
}

func echo(w http.ResponseWriter, r *http.Request) {
    conn, err := upgrader.Upgrade(w, r, nil)
    if err != nil {
        return
    }
    defer conn.Close()

    for {
        msgType, msg, err := conn.ReadMessage()
        if err != nil {
            break
        }
        conn.WriteMessage(msgType, []byte("收到: "+string(msg)))
    }
}

func main() {
    http.HandleFunc("/ws", echo)
    http.ListenAndServe(":8080", nil)
}
```

### 客户端（浏览器 JS）

```js
let ws = new WebSocket("ws://localhost:8080/ws");
ws.onmessage = (e) => console.log(e.data);
ws.send("hello");
```

### 典型场景

- 聊天室
- 实时通知
- 股票行情推送
- 协同编辑

## 核心重点
- WebSocket 全双工长连接，服务端可推送
- gorilla/websocket 的 Upgrader.Upgrade 升级 HTTP
- ReadMessage 读、WriteMessage 写
- 连接用完 Close
- 适合实时双向通信

## 练习/任务
1. 跑通 WebSocket echo 服务
2. 用浏览器 JS 连上去发消息
3. 扩展成简单聊天室：维护连接池，广播消息
4. 加心跳检测（定期 ping/pong）

## 今日刷题

### 1. LeetCode 200 岛屿数量（复习）
- 思路：DFS。
- 代码提示：沉没。
- Go 要点：网格。

### 2. LeetCode 133 克隆图（复习）
- 思路：DFS+map。
- 代码提示：避免重复。
- Go 要点：图。

## 打卡清单
- [ ] 理解 WebSocket 与 HTTP 区别
- [ ] 会用 gorilla/websocket
- [ ] 会双向收发消息
- [ ] 复习岛屿与克隆图
