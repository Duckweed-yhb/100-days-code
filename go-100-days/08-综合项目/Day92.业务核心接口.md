# Day 92 · 业务核心接口

## 今日目标
- 实现待办的增删改查
- 按用户隔离数据
- 完成核心业务闭环

## 学习内容

### 列出当前用户待办

```go
func ListTodos(c *gin.Context) {
    uid := c.GetFloat64("uid")
    rows, err := db.Query(`SELECT id, title, done, created_at FROM todos WHERE user_id=?`, int(uid))
    if err != nil {
        c.JSON(500, gin.H{"error": err.Error()})
        return
    }
    defer rows.Close()

    var todos []Todo
    for rows.Next() {
        var t Todo
        var done int
        rows.Scan(&t.ID, &t.Title, &done, &t.CreatedAt)
        t.Done = done == 1
        todos = append(todos, t)
    }
    c.JSON(200, todos)
}
```

### 新建待办

```go
func CreateTodo(c *gin.Context) {
    var req struct {
        Title string `json:"title" binding:"required"`
    }
    if err := c.ShouldBindJSON(&req); err != nil {
        c.JSON(400, gin.H{"error": err.Error()})
        return
    }
    uid := c.GetFloat64("uid")
    res, _ := db.Exec(`INSERT INTO todos(user_id, title, created_at) VALUES(?,?,?)`,
        int(uid), req.Title, time.Now())
    id, _ := res.LastInsertId()
    c.JSON(200, gin.H{"id": id, "title": req.Title, "done": false})
}
```

### 更新与删除

```go
func UpdateTodo(c *gin.Context) {
    id := c.Param("id")
    var req struct {
        Title *string `json:"title"`
        Done  *bool   `json:"done"`
    }
    c.ShouldBindJSON(&req)
    // 动态更新字段（略）
}

func DeleteTodo(c *gin.Context) {
    id := c.Param("id")
    db.Exec(`DELETE FROM todos WHERE id=?`, id)
    c.JSON(200, gin.H{"deleted": id})
}
```

### 注册路由

```go
auth := r.Group("/", AuthRequired())
{
    auth.GET("/todos", ListTodos)
    auth.POST("/todos", CreateTodo)
    auth.PUT("/todos/:id", UpdateTodo)
    auth.DELETE("/todos/:id", DeleteTodo)
}
```

## 核心重点
- 每个查询都带 user_id 隔离数据
- 用 c.GetFloat64 取中间件存的 uid
- 绑定请求体做校验
- 只操作自己的待办

## 练习/任务
1. 实现四个待办接口
2. 登录后新建几个待办
3. 验证用户 A 看不到用户 B 的待办
4. 更新一个待办为 done=true

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
- [ ] 四个待办接口跑通
- [ ] 用户数据隔离正确
- [ ] 完整 CRUD 闭环
- [ ] 复习 LRU 与岛屿
