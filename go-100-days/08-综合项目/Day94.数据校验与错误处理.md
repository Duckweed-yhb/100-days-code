# Day 94 · 数据校验与错误处理

## 今日目标
- 完善请求参数校验
- 统一处理业务错误
- 避免向用户泄露内部错误

## 学习内容

### 请求校验

```go
type CreateTodoRequest struct {
    Title string `json:"title" binding:"required,min=1,max=100"`
}
```

校验失败 gin 自动返回错误，我们包装一下：

```go
if err := c.ShouldBindJSON(&req); err != nil {
    c.JSON(400, gin.H{"error": "参数无效: " + err.Error()})
    return
}
```

### 业务错误

```go
var ErrNotFound = errors.New("待办不存在")

func UpdateTodo(c *gin.Context) {
    err := updateTodoInDB()
    if errors.Is(err, ErrNotFound) {
        c.JSON(404, gin.H{"error": "待办不存在"})
        return
    }
    if err != nil {
        log.Println("内部错误:", err)
        c.JSON(500, gin.H{"error": "服务器内部错误"})
        return
    }
}
```

### 不要泄露内部错误

500 错误只返回通用提示，详细错误打日志。不要把 SQL 错误直接抛给前端。

### 404 处理

```go
r.NoRoute(func(c *gin.Context) {
    c.JSON(404, gin.H{"error": "路径不存在"})
})
```

### 常用状态码

| 码 | 含义 |
|---|---|
| 200 | 成功 |
| 400 | 参数错误 |
| 401 | 未登录 |
| 403 | 无权限 |
| 404 | 不存在 |
| 500 | 服务器错误 |

## 核心重点
- binding tag 做参数校验
- 业务错误用 errors.Is 判断
- 500 不泄露内部细节
- NoRoute 处理 404
- 状态码语义要准

## 练习/任务
1. 给所有请求加校验
2. 用 errors.Is 处理业务错误
3. 加 NoRoute 404
4. 故意触发 500，确认前端看不到 SQL 错误

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
- [ ] 参数校验完善
- [ ] 业务错误正确分类
- [ ] 不泄露内部错误
- [ ] 复习 LRU 与岛屿
