# Day 67 · HTTP服务端

## 今日目标
- 用 net/http 启动一个 Web 服务
- 学会注册路由、写 handler
- 理解 Request 与 ResponseWriter

## 学习内容

### 最简 HTTP 服务

```go
package main

import (
    "fmt"
    "net/http"
)

func hello(w http.ResponseWriter, r *http.Request) {
    fmt.Fprintf(w, "你好, %s!", r.URL.Path)
}

func main() {
    http.HandleFunc("/hello", hello)
    http.ListenAndServe(":8080", nil)
}
```

访问 http://localhost:8080/hello 即可。

### Handler 签名

```go
func(w http.ResponseWriter, r *http.Request)
```

- `w`：往客户端写响应
- `r`：请求信息（方法、路径、头、Body）

### 读请求信息

```go
r.Method            // GET/POST
r.URL.Path          // 路径
r.URL.Query().Get("name")  // 查询参数 ?name=Bob
r.Header.Get("Content-Type")
```

### 写响应

```go
w.Write([]byte("响应体"))
w.Header().Set("Content-Type", "application/json")
w.WriteHeader(http.StatusOK)
```

### 静态文件

```go
http.Handle("/static/", http.StripPrefix("/static/", http.FileServer(http.Dir("public"))))
```

## 核心重点
- http.HandleFunc 注册路由
- ListenAndServe 启动服务
- handler 收 (w, r)
- 读参数用 r.URL.Query，写响应用 w.Write
- net/http 自带路由，简单服务够用

## 练习/任务
1. 跑通最简 HTTP 服务
2. 加一个 /name?name=Bob 路由，返回 "你好 Bob"
3. 写一个返回 JSON 的 handler
4. 用 curl 或浏览器测试每个路由

## 今日刷题

### 1. LeetCode 20 有效的括号（复习）
- 思路：栈。
- 代码提示：map 配对。
- Go 要点：栈。

### 2. LeetCode 21 合并两个有序链表（复习）
- 思路：哑节点。
- 代码提示：谁小接谁。
- Go 要点：链表。

## 打卡清单
- [ ] 会启动 HTTP 服务
- [ ] 会注册路由写 handler
- [ ] 会读查询参数
- [ ] 复习括号与链表
