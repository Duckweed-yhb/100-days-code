# Day 89 · 项目规划与数据模型

## 今日目标
- 选定综合项目：一个待办事项 API
- 设计数据模型与表结构
- 规划接口列表

## 学习内容

### 项目：Todo List API

一个完整的待办事项后端：用户注册登录、增删改查待办、按状态筛选。

### 数据模型

```sql
CREATE TABLE users (
    id INTEGER PRIMARY KEY AUTOINCREMENT,
    username TEXT UNIQUE,
    password TEXT
);

CREATE TABLE todos (
    id INTEGER PRIMARY KEY AUTOINCREMENT,
    user_id INTEGER,
    title TEXT,
    done INTEGER DEFAULT 0,
    created_at DATETIME
);
```

### Go 结构体

```go
type User struct {
    ID       int    `json:"id"`
    Username string `json:"username"`
    Password string `json:"-"`
}

type Todo struct {
    ID        int    `json:"id"`
    Title     string `json:"title"`
    Done      bool   `json:"done"`
    CreatedAt string `json:"created_at"`
}
```

### 接口列表

| 方法 | 路径 | 说明 |
|---|---|---|
| POST | /signup | 注册 |
| POST | /login | 登录返回 JWT |
| GET | /todos | 列出当前用户待办 |
| POST | /todos | 新建待办 |
| PUT | /todos/:id | 更新待办 |
| DELETE | /todos/:id | 删除待办 |

### 项目结构

```
todo-api/
  main.go
  go.mod
  internal/
    handler/
    model/
    db/
    middleware/
```

## 核心重点
- 先设计数据模型再写代码
- 接口列表要清晰
- 项目按包组织
- 密码不返回给前端（json:"-"）

## 练习/任务
1. 画出 ER 图
2. 列出所有接口与请求响应格式
3. 建项目目录结构
4. 写好 User 与 Todo 结构体

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
- [ ] 完成数据模型设计
- [ ] 列好接口清单
- [ ] 建好项目结构
- [ ] 复习括号与链表
