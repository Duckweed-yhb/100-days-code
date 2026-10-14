# Day 88 · Web工程化阶段练习

## 今日目标
- 综合 gin + 中间件 + JWT + 数据库写一个完整 API
- 把 Web 工程化要点串起来
- 完成阶段七算法练习

## 学习内容

### 项目：一个带鉴权的用户管理 API

需求：注册、登录、查看个人资料，用 SQLite 存储，JWT 鉴权。

```go
package main

import (
    "database/sql"
    "net/http"
    "time"

    "github.com/gin-gonic/gin"
    "github.com/golang-jwt/jwt/v5"
    _ "github.com/mattn/go-sqlite3"
)

var db *sql.DB
var secret = []byte("my-secret")

func initDB() {
    db, _ = sql.Open("sqlite3", "./users.db")
    db.Exec(`CREATE TABLE IF NOT EXISTS users (
        id INTEGER PRIMARY KEY AUTOINCREMENT,
        username TEXT UNIQUE,
        password TEXT
    )`)
}

func signup(c *gin.Context) {
    var req struct {
        Username string `json:"username" binding:"required,min=3"`
        Password string `json:"password" binding:"required,min=6"`
    }
    if err := c.ShouldBindJSON(&req); err != nil {
        c.JSON(400, gin.H{"error": err.Error()})
        return
    }
    res, err := db.Exec(`INSERT INTO users(username, password) VALUES(?, ?)`,
        req.Username, req.Password)
    if err != nil {
        c.JSON(409, gin.H{"error": "用户名已存在"})
        return
    }
    id, _ := res.LastInsertId()
    c.JSON(200, gin.H{"id": id, "username": req.Username})
}

func login(c *gin.Context) {
    var req struct {
        Username string `json:"username"`
        Password string `json:"password"`
    }
    c.ShouldBindJSON(&req)
    var id int
    err := db.QueryRow(`SELECT id FROM users WHERE username=? AND password=?`,
        req.Username, req.Password).Scan(&id)
    if err != nil {
        c.JSON(401, gin.H{"error": "用户名或密码错误"})
        return
    }
    token := jwt.NewWithClaims(jwt.SigningMethodHS256, jwt.MapClaims{
        "uid":  id,
        "exp":  time.Now().Add(24 * time.Hour).Unix(),
    })
    s, _ := token.SignedString(secret)
    c.JSON(200, gin.H{"token": s})
}

func profile(c *gin.Context) {
    tokenStr := c.GetHeader("Authorization")
    token, err := jwt.Parse(tokenStr, func(t *jwt.Token) (any, error) {
        return secret, nil
    })
    if err != nil || !token.Valid {
        c.JSON(401, gin.H{"error": "未授权"})
        return
    }
    claims := token.Claims.(jwt.MapClaims)
    c.JSON(200, gin.H{"user_id": claims["uid"]})
}

func main() {
    initDB()
    r := gin.Default()
    r.POST("/signup", signup)
    r.POST("/login", login)
    r.GET("/profile", profile)
    r.Run(":8080")
}
```

这就是一个完整的后端 API：注册、登录、鉴权、数据库。

## 核心重点
- gin + SQLite + JWT 是 Web 后端三件套
- 绑定参数做校验
- 错误返回合适状态码
- 鉴权中间件保护敏感接口

## 练习/任务
1. 跑通用户管理 API
2. 把 profile 改成用中间件统一鉴权
3. 加一个修改密码接口
4. 用 curl 完整测试注册→登录→查资料流程

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
- [ ] 跑通用户管理 API
- [ ] 完成注册登录鉴权流程
- [ ] 复习 LRU 与岛屿
- [ ] 阶段七所有概念亲手写过
