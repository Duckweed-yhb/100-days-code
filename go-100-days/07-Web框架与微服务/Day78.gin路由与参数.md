# Day 78 · gin路由与参数

## 今日目标
- 掌握路径参数、查询参数、表单
- 学会路由分组
- 理解 gin 的路由树

## 学习内容

### 路径参数

```go
r.GET("/users/:id", func(c *gin.Context) {
    id := c.Param("id")
    c.JSON(200, gin.H{"id": id})
})
```

### 查询参数

```go
// /search?q=go&page=1
r.GET("/search", func(c *gin.Context) {
    q := c.DefaultQuery("q", "默认")
    page := c.Query("page")
})
```

### 路由分组

```go
api := r.Group("/api/v1")
{
    api.GET("/users", listUsers)
    api.POST("/users", createUser)

    auth := api.Group("/admin")
    {
        auth.GET("/stats", stats)
    }
}
```

URL 变成 `/api/v1/users`、`/api/v1/admin/stats`。

### 静态文件

```go
r.Static("/static", "./public")
```

### 重定向

```go
r.Redirect(302, "https://example.com")
```

## 核心重点
- c.Param 取路径参数
- c.Query 取查询参数
- Group 分组管理路由
- 分组可嵌套
- Static 托管静态文件

## 练习/任务
1. 写 /users/:id 接口返回 id
2. 写 /search?keyword=xx 接口
3. 建 /api/v1 分组，里面放用户和订单路由
4. 托管一个静态目录

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
- [ ] 会用路径参数与查询参数
- [ ] 会路由分组
- [ ] 会托管静态文件
- [ ] 复习链表题
