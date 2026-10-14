# Day 76 · HTTP深入与路由

## 今日目标
- 理解 net/http 的 ServeMux
- 学会路由分组与方法匹配
- 认识为何需要第三方路由框架

## 学习内容

### ServeMux

`http.DefaultServeMux` 是默认路由器，按路径前缀匹配。

```go
http.HandleFunc("/api/", apiHandler)   // 前缀匹配
http.HandleFunc("/about", aboutHandler)
```

### 标准路由的局限

- 不支持路径参数 `/users/:id`
- 不区分 GET/POST（要在 handler 里自己判断）
- 不支持路由分组

### 自己判断方法

```go
func handler(w http.ResponseWriter, r *http.Request) {
    if r.Method != http.MethodGet {
        http.Error(w, "只支持 GET", 405)
        return
    }
}
```

### 路由参数

标准库要自己解析 URL：

```go
// /users/123
// 手动 split r.URL.Path
```

Go 1.22 增强了 ServeMux，支持方法与路径参数：

```go
mux := http.NewServeMux()
mux.HandleFunc("GET /users/{id}", func(w http.ResponseWriter, r *http.Request) {
    id := r.PathValue("id")
})
```

### 为什么还用 gin

即使 Go 1.22 改进了路由，gin 在中间件、参数绑定、生态上仍更方便，工业界主流。

## 核心重点
- ServeMux 按路径匹配
- 标准路由要自己判 Method
- Go 1.22 支持 `GET /users/{id}` 与 PathValue
- gin 等框架提供更好用的中间件与绑定
- 小服务用标准库够用，大服务上框架

## 练习/任务
1. 用标准库写两个路由
2. 用 Go 1.22 新语法写 GET /users/{id}
3. 在 handler 里判断 Method
4. 体会标准库路由的繁琐

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
- [ ] 理解 ServeMux
- [ ] 会用 Go 1.22 路由参数
- [ ] 知道标准库路由局限
- [ ] 复习括号与链表
