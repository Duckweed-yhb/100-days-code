# Day 79 · gin请求绑定校验

## 今日目标
- 学会用 ShouldBindJSON 绑定请求体
- 掌握 binding tag 做参数校验
- 处理绑定错误

## 学习内容

### 定义请求结构体

```go
type CreateUserRequest struct {
    Name string `json:"name" binding:"required,min=2,max=20"`
    Age  int    `json:"age" binding:"required,min=0,max=150"`
}
```

### 绑定请求

```go
r.POST("/users", func(c *gin.Context) {
    var req CreateUserRequest
    if err := c.ShouldBindJSON(&req); err != nil {
        c.JSON(400, gin.H{"error": err.Error()})
        return
    }
    // req 已填好并校验过
    c.JSON(200, gin.H{"received": req})
})
```

### 常用 binding tag

| tag | 作用 |
|---|---|
| `required` | 必填 |
| `min=N` / `max=N` | 最小/最大 |
| `email` | 邮箱格式 |
| `oneof=red blue` | 枚举 |
| `omitempty` | 可空 |

### 表单与查询绑定

```go
c.ShouldBindQuery(&req)   // 绑定 URL 查询参数
c.ShouldBind(&req)        // 自动判断
```

### 错误处理

绑定失败要返回 400，不要继续往下走。

## 核心重点
- ShouldBindJSON 把 Body 绑定到 struct
- binding tag 做校验
- 绑定失败返回 400
- binding:"required,min=2" 组合使用
- ShouldBindQuery 绑查询参数

## 练习/任务
1. 写一个创建用户接口，校验 name 必填、age 范围
2. 故意传缺字段的 JSON，看 400 响应
3. 加 email 字段用 binding:"email" 校验
4. 写一个查询参数绑定接口

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
- [ ] 会用 ShouldBindJSON
- [ ] 会写 binding 校验 tag
- [ ] 会处理绑定错误
- [ ] 复习括号与链表
