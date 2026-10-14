# Day 69 · HTTP客户端高级

## 今日目标
- 学会 POST、自定义 Header
- 掌握 http.NewRequest
- 理解常用 Client 配置

## 学习内容

### POST JSON

```go
data := `{"name":"Bob","age":20}`
req, err := http.NewRequest("POST", "https://api.example.com/users", strings.NewReader(data))
if err != nil {
    log.Fatal(err)
}
req.Header.Set("Content-Type", "application/json")
req.Header.Set("Authorization", "Bearer token123")

client := &http.Client{Timeout: 10 * time.Second}
resp, err := client.Do(req)
defer resp.Body.Close()
```

### NewRequest vs http.Get

`http.Get` 是便捷函数；`NewRequest` + `client.Do` 能完全控制方法、Header、Body。

### 表单 POST

```go
form := url.Values{}
form.Set("name", "Bob")
resp, _ := http.PostForm("https://example.com", form)
```

### 常用 Header

- `Content-Type`：application/json、application/x-www-form-urlencoded
- `Authorization`：Bearer token
- `User-Agent`

### 并发请求

```go
urls := []string{"...", "...", "..."}
var wg sync.WaitGroup
for _, u := range urls {
    wg.Add(1)
    go func(url string) {
        defer wg.Done()
        resp, err := http.Get(url)
        if err == nil {
            defer resp.Body.Close()
            // ...
        }
    }(u)
}
wg.Wait()
```

## 核心重点
- NewRequest + Do 完全控制请求
- POST 用 strings.NewReader 做 Body
- Header.Set 加请求头
- PostForm 发表单
- 并发抓取要 WaitGroup

## 练习/任务
1. 写一个 POST JSON 请求
2. 加 Authorization 头
3. 并发请求 3 个 URL，汇总状态码
4. 用 PostForm 提交表单

## 今日刷题

### 1. LeetCode 20 有效的括号（复习）
- 思路：栈。
- 代码提示：map 配对。
- Go 要点：栈。

### 2. LeetCode 217 存在重复元素（复习）
- 思路：map。
- 代码提示：set。
- Go 要点：map。

## 打卡清单
- [ ] 会发 POST JSON
- [ ] 会加自定义 Header
- [ ] 会并发抓取
- [ ] 复习括号与重复元素
