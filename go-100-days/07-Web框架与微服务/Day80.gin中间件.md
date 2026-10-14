# Day 80 · gin中间件

## 今日目标
- 理解中间件是请求拦截器
- 学会写日志、鉴权中间件
- 掌握全局与路由级中间件

## 学习内容

### 中间件是什么

中间件是一个 `func(c *gin.Context)`，在 handler 前后执行，类似过滤器。

### 写一个日志中间件

```go
func Logger() gin.HandlerFunc {
    return func(c *gin.Context) {
        start := time.Now()
        c.Next()    // 执行后续 handler
        latency := time.Since(start)
        log.Printf("%s %s 耗时 %v", c.Request.Method, c.Request.URL, latency)
    }
}
```

### 使用中间件

```go
r := gin.New()
r.Use(Logger())              // 全局中间件

// 路由级
api := r.Group("/api", AuthRequired())
{
    api.GET("/profile", profile)
}
```

### 鉴权中间件

```go
func AuthRequired() gin.HandlerFunc {
    return func(c *gin.Context) {
        token := c.GetHeader("Authorization")
        if token == "" {
            c.AbortWithStatusJSON(401, gin.H{"error": "未登录"})
            return
        }
        c.Next()
    }
}
```

`c.Abort()` 阻止后续 handler 执行。

### Context 传值

```go
c.Set("userID", 42)      // 中间件存
userID := c.GetInt("userID")  // handler 取
```

## 核心重点
- 中间件是 handler 前后的钩子
- c.Next() 继续，c.Abort() 中断
- r.Use 全局，Group 级局部
- 鉴权、日志、CORS 都用中间件
- c.Set/Get 在中间件与 handler 间传值

## 练习/任务
1. 写一个请求日志中间件
2. 写一个 Token 鉴权中间件
3. 给 /api 分组加鉴权，/public 不加
4. 在中间件里把 userID 传到 handler

## 今日刷题

### 1. LeetCode 146 LRU 缓存（复习）
- 思路：map+双向链表。
- 代码提示：list.List。
- Go 要点：设计。

### 2. LeetCode 200 岛屿数量（复习）
- 思路：DFS。
- 代码提示：沉没。
- Go 要点：网格。

## 打卡清单
- [ ] 会写中间件
- [ ] 会用 Next/Abort
- [ ] 会写鉴权中间件
- [ ] 复习 LRU 与岛屿
