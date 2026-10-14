# Day 93 · 中间件与鉴权

## 今日目标
- 完善中间件体系
- 加请求日志与恢复
- 统一错误响应格式

## 学习内容

### gin 自带中间件

```go
r := gin.New()
r.Use(gin.Logger())       // 请求日志
r.Use(gin.Recovery())     // panic 恢复
```

### 请求日志中间件

```go
func RequestLogger() gin.HandlerFunc {
    return func(c *gin.Context) {
        start := time.Now()
        c.Next()
        log.Printf("%s %s %d 耗时%v",
            c.Request.Method, c.Request.URL.Path, c.Writer.Status(), time.Since(start))
    }
}
```

### 统一错误格式

```go
func ErrorResponse(c *gin.Context, code int, msg string) {
    c.JSON(code, gin.H{
        "error": gin.H{
            "code":    code,
            "message": msg,
        },
    })
}
```

### CORS 跨域

前后端分离必备：

```go
func CORS() gin.HandlerFunc {
    return func(c *gin.Context) {
        c.Header("Access-Control-Allow-Origin", "*")
        c.Header("Access-Control-Allow-Methods", "GET, POST, PUT, DELETE")
        c.Header("Access-Control-Allow-Headers", "Content-Type, Authorization")
        if c.Request.Method == "OPTIONS" {
            c.AbortWithStatus(204)
            return
        }
        c.Next()
    }
}
```

### 注册顺序

```go
r := gin.New()
r.Use(gin.Logger(), gin.Recovery(), CORS(), RequestLogger())
```

中间件按注册顺序执行。

## 核心重点
- Logger/Recovery 是基础中间件
- 统一错误响应格式
- CORS 解决跨域
- 中间件顺序有意义
- panic 不崩服务

## 练习/任务
1. 给项目加 Logger 与 Recovery
2. 加 CORS 中间件
3. 统一错误响应格式
4. 故意触发 panic，看 Recovery 是否兜住

## 今日刷题

### 1. LeetCode 20 有效的括号（复习）
- 思路：栈。
- 代码提示：map。
- Go 要点：栈。

### 2. LeetCode 21 合并两个有序链表（复习）
- 思路：哑节点。
- 代码提示：谁小接谁。
- Go 要点：链表。

## 打卡清单
- [ ] 加好日志与恢复
- [ ] CORS 生效
- [ ] 错误格式统一
- [ ] 复习括号与链表
