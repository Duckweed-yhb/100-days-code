# Day 46 · JSON进阶与流式

## 今日目标
- 学会处理不确定结构的 JSON（map[string]any）
- 掌握 Encoder/Decoder 流式编解码
- 熟悉 json.RawMessage 延迟解析

## 学习内容

### 不确定结构：用 map

```go
raw := []byte(`{"name":"Bob","tags":["a","b"]}`)
var m map[string]any
json.Unmarshal(raw, &m)
name := m["name"].(string)
```

从 any 取值要做类型断言。

### Encoder / Decoder（流式）

从 HTTP 请求体或文件直接读写 JSON，不用先把整个内容读进内存：

```go
// 写 JSON 到 Writer（如 HTTP Response）
enc := json.NewEncoder(w)
enc.Encode(user)

// 从 Reader 读 JSON
dec := json.NewDecoder(r)
var u User
dec.Decode(&u)
```

Web 框架里 `c.ShouldBind` 底层就是这玩意。

### json.RawMessage 延迟解析

有些字段类型要根据别的字段决定，可以先存成 RawMessage 再二次解析：

```go
type Event struct {
    Type string          `json:"type"`
    Data json.RawMessage `json:"data"`
}
```

### 缩进输出

```go
data, _ := json.MarshalIndent(u, "", "  ")
// 美化过的 JSON
```

### 错误处理

Unmarshal 失败返回 `*json.SyntaxError` 或类型不匹配错误，要检查 err。

## 核心重点
- 不确定结构用 map[string]any + 类型断言
- Encoder/Decoder 流式处理，省内存
- RawMessage 延迟解析动态字段
- MarshalIndent 美化输出
- 反序列化失败要查 err

## 练习/任务
1. 解析一个字段类型不定的 JSON（根据 type 字段二次解析）
2. 用 Encoder 把 struct 直接写到 os.Stdout
3. 用 Decoder 从字符串 Reader 读 JSON
4. 用 MarshalIndent 美化输出

## 今日刷题

### 1. LeetCode 121 买卖股票最佳时机
- 思路：一次遍历，维护最低价，每天算今天卖能赚多少。
- 代码提示：`minPrice, maxProfit` 两个变量。
- Go 要点：一维 DP 思想。

### 2. LeetCode 53 最大子数组和
- 思路：Kadane，curr = max(n, curr+n)，ans = max(ans, curr)。
- 代码提示：从第一个元素开始。
- Go 要点：动态规划入门。

## 打卡清单
- [ ] 会用 map[string]any 解析动态 JSON
- [ ] 会用 Encoder/Decoder 流式处理
- [ ] 理解 RawMessage
- [ ] 写完股票与最大子数组
