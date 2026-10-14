# Day 77 · gin框架入门

## 今日目标
- 安装 gin 并启动第一个 Web 服务
- 理解 gin 的 Engine / RouterGroup / Context
- 学会写 GET/POST 接口

## 学习内容

### 安装

```powershell
go get -u github.com/gin-gonic/gin
```

### 第一个 gin 服务

```go
package main

import "github.com/gin-gonic/gin"

func main() {
    r := gin.Default()

    r.GET("/ping", func(c *gin.Context) {
        c.JSON(200, gin.H{"message": "pong"})
    })

    r.Run(":8080")
}
```

访问 http://localhost:8080/ping 返回 JSON。

### 核心概念

| 概念 | 作用 |
|---|---|
| Engine | 引擎，最外层路由容器 |
| RouterGroup | 路由分组（如 /api/v1） |
| Context | 每个请求的上下文，含请求与响应 |

### gin.H

`gin.H` 就是 `map[string]any` 的别名，方便快速构造 JSON。

### GET/POST

```go
r.GET("/users", listUsers)
r.POST("/users", createUser)
r.PUT("/users/:id", updateUser)
r.DELETE("/users/:id", deleteUser)
```

### Context 的常用方法

- `c.JSON(状态码, 数据)` 返回 JSON
- `c.Param("id")` 路径参数
- `c.Query("name")` 查询参数
- `c.ShouldBindJSON(&obj)` 绑定 Body

## 核心重点
- gin.Default() 建引擎
- r.GET/POST/PUT/DELETE 注册路由
- c.JSON 返回 JSON
- gin.H 快速构造 map
- gin 是 Go 最主流 Web 框架

## 练习/任务
1. 跑通第一个 gin 服务
2. 写一个 /hello?name=Bob 返回 JSON
3. 写一个 POST 接口接收 JSON
4. 用 curl 测试所有接口

## 今日刷题

### 1. LeetCode 1 两数之和（复习）
- 思路：map。
- 代码提示：查 target-x。
- Go 要点：map。

### 2. LeetCode 88 合并两个有序数组（复习）
- 思路：从后往前。
- 代码提示：三指针。
- Go 要点：数组。

## 打卡清单
- [ ] 会启动 gin 服务
- [ ] 会写 GET/POST
- [ ] 会用 c.JSON 返回
- [ ] 复习两数之和
