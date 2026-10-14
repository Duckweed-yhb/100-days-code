# Day 68 · HTTP客户端Get

## 今日目标
- 用 net/http 发 GET 请求
- 学会读响应状态码与 Body
- 处理错误与超时

## 学习内容

### 最简单的 GET

```go
resp, err := http.Get("https://api.github.com/users/octocat")
if err != nil {
    log.Fatal(err)
}
defer resp.Body.Close()

body, err := io.ReadAll(resp.Body)
fmt.Println(resp.StatusCode)
fmt.Println(string(body))
```

### 关键步骤

1. `http.Get` 发请求
2. 检查 err
3. `defer resp.Body.Close()`
4. 读 `resp.Body`

### 状态码

```go
if resp.StatusCode != http.StatusOK {
    log.Fatal("状态码异常:", resp.StatusCode)
}
```

### 带超时的 Client

默认 http.Client 没超时，生产必须自己设：

```go
client := &http.Client{
    Timeout: 10 * time.Second,
}
resp, err := client.Get("https://example.com")
```

### 解析 JSON 响应

```go
var user struct {
    Login string `json:"login"`
}
json.NewDecoder(resp.Body).Decode(&user)
```

## 核心重点
- http.Get 发请求，defer Close Body
- 检查 StatusCode
- 用自定义 Client 设超时
- Decoder 直接从 Body 解析 JSON
- 不要用默认无超时 Client

## 练习/任务
1. 用 http.Get 请求一个公开 API 并打印
2. 把 JSON 响应解析到 struct
3. 给 Client 加 5 秒超时
4. 请求一个不存在的 URL，观察错误处理

## 今日刷题

### 1. LeetCode 1 两数之和（复习）
- 思路：map。
- 代码提示：查 target-x。
- Go 要点：map。

### 2. LeetCode 88 合并两个有序数组（复习）
- 思路：从后往前。
- 代码提示：三指针。
- Go 要点：数组。

## 打卡清单
- [ ] 会发 GET 请求
- [ ] 会读响应 Body
- [ ] 会设超时
- [ ] 复习两数之和
