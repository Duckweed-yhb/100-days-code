# Day 45 · JSON序列化

## 今日目标
- 掌握 struct 与 JSON 互相转换
- 理解 struct tag 的作用
- 学会处理嵌套结构与省略字段

## 学习内容

### 序列化 Marshal

```go
type User struct {
    Name string `json:"name"`
    Age  int    `json:"age"`
}

u := User{Name: "Bob", Age: 20}
data, err := json.Marshal(u)
// data = {"name":"Bob","age":20}
fmt.Println(string(data))
```

### struct tag

反引号里的 `json:"name"` 指定 JSON 字段名。常用选项：

| tag | 效果 |
|---|---|
| `json:"name"` | 字段名映射 |
| `json:"-"` | 完全忽略该字段 |
| `json:",omitempty"` | 零值时省略 |
| `json:"name,omitempty"` | 命名 + 零值省略 |

### 反序列化 Unmarshal

```go
raw := []byte(`{"name":"Alice","age":30}`)
var u User
err := json.Unmarshal(raw, &u)
fmt.Println(u.Name)   // Alice
```

注意传**指针**。

### 嵌套结构

```go
type Profile struct {
    City string `json:"city"`
}
type User struct {
    Name    string  `json:"name"`
    Profile Profile `json:"profile"`
}
```

### map 与任意 JSON

```go
var m map[string]any
json.Unmarshal(raw, &m)
// 数字默认 float64
```

## 核心重点
- `json.Marshal` 序列化，`json.Unmarshal` 反序列化
- 反序列化传指针
- struct tag `json:"name"` 控制字段名
- `omitempty` 零值省略
- 数字默认 float64，注意整数精度

## 练习/任务
1. 定义 User 结构体序列化成 JSON 字符串
2. 把一段 JSON 反序列化到 struct
3. 给 Age 加 omitempty，Age=0 时观察输出
4. 解析一个嵌套 JSON（用户含地址）

## 今日刷题

### 1. LeetCode 136 只出现一次的数字（复习）
- 思路：异或。
- 代码提示：全部数异或。
- Go 要点：位运算。

### 2. LeetCode 169 多数元素
- 思路：Boyer-Moore 投票，或 map 计数。
- 代码提示：`count := map[int]int{}`，找 > n/2。
- Go 要点：map 计数。

## 打卡清单
- [ ] 会 Marshal / Unmarshal
- [ ] 会用 struct tag
- [ ] 理解 omitempty
- [ ] 写完多数元素
