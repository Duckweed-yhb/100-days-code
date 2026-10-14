# Day 96 · 接口集成测试

## 今日目标
- 用 httptest 测试 HTTP 接口
- 模拟完整请求流程
- 覆盖注册→登录→待办闭环

## 学习内容

### httptest 包

标准库 `net/http/httptest` 用来模拟请求：

```go
func TestListTodos(t *testing.T) {
    r := setupRouter()

    w := httptest.NewRecorder()
    req := httptest.NewRequest("GET", "/todos", nil)
    req.Header.Set("Authorization", "Bearer "+token)

    r.ServeHTTP(w, req)

    if w.Code != 200 {
        t.Errorf("状态码 = %d", w.Code)
    }
}
```

### 测试注册登录闭环

```go
func TestSignupLogin(t *testing.T) {
    r := setupRouter()

    // 注册
    body := `{"username":"test","password":"123456"}`
    req := httptest.NewRequest("POST", "/signup", strings.NewReader(body))
    req.Header.Set("Content-Type", "application/json")
    w := httptest.NewRecorder()
    r.ServeHTTP(w, req)
    // 断言 200

    // 登录、建待办、列表……
}
```

### 集成 vs 单元

- 单元：测单个函数，快
- 集成：测整个 HTTP 流程，更真实

## 核心重点
- httptest.NewRecorder + NewRequest 模拟请求
- r.ServeHTTP 触发路由
- 集成测试覆盖完整业务闭环
- 单元测试快，集成测试真

## 练习/任务
1. 写一个接口测试：注册后能登录
2. 写一个接口测试：登录后能建待办并列出
3. 测未带 token 访问 /todos 返回 401
4. 跑 `go test ./...`

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
- [ ] 会用 httptest
- [ ] 覆盖核心闭环
- [ ] 集成测试通过
- [ ] 复习 LRU 与岛屿
