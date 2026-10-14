# Day 90 · 搭建项目骨架

## 今日目标
- 初始化 go module
- 搭好 gin + SQLite 骨架
- 写好启动与优雅关闭

## 学习内容

### 初始化

```powershell
mkdir todo-api
cd todo-api
go mod init example.com/todo-api
go get github.com/gin-gonic/gin
go get github.com/golang-jwt/jwt/v5
go get github.com/mattn/go-sqlite3
```

### main.go 骨架

```go
package main

import (
    "context"
    "log"
    "net/http"
    "os"
    "os/signal"
    "syscall"
    "time"

    "github.com/gin-gonic/gin"
)

func main() {
    r := gin.Default()

    // TODO: 注册路由
    // r.GET("/todos", listTodos)

    srv := &http.Server{
        Addr:    ":8080",
        Handler: r,
    }

    go func() {
        if err := srv.ListenAndServe(); err != nil && err != http.ErrServerClosed {
            log.Fatal(err)
        }
    }()

    quit := make(chan os.Signal, 1)
    signal.Notify(quit, syscall.SIGINT, syscall.SIGTERM)
    <-quit

    ctx, cancel := context.WithTimeout(context.Background(), 5*time.Second)
    defer cancel()
    srv.Shutdown(ctx)
}
```

### 数据库初始化

单独放 `internal/db/db.go`：

```go
package db

import "database/sql"

func Init(path string) (*sql.DB, error) {
    d, err := sql.Open("sqlite3", path)
    if err != nil {
        return nil, err
    }
    d.Exec(`CREATE TABLE IF NOT EXISTS users (...)`)
    d.Exec(`CREATE TABLE IF NOT EXISTS todos (...)`)
    return d, nil
}
```

## 核心重点
- 先把骨架跑起来
- 优雅关闭
- 数据库初始化独立包
- 路由后续逐步加

## 练习/任务
1. 初始化项目并拉依赖
2. 写好 main.go 骨架
3. 写好 db.Init
4. 跑起来确认能启动、能 Ctrl+C 退出

## 今日刷题

### 1. LeetCode 146 LRU 缓存（复习）
- 思路：map+链表。
- 代码提示：list.List。
- Go 要点：设计。

### 2. LeetCode 200 岛屿数量（复习）
- 思路：DFS。
- 代码提示：沉没。
- Go 要点：网格。

## 打卡清单
- [ ] 项目骨架跑通
- [ ] 优雅关闭生效
- [ ] 数据库初始化完成
- [ ] 复习 LRU 与岛屿
