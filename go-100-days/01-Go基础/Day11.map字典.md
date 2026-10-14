# Day 11 · map字典

## 今日目标
- 掌握 map 的声明、读写、遍历
- 理解 key 的要求与零值
- 学会判断 key 是否存在、安全删除

## 学习内容

### map 声明

map 是无序键值表，必须用 `make` 或字面量创建，**不能用 new 出来的 nil map 直接写**：

```go
m := make(map[string]int)           // 空 map
m2 := map[string]int{"a": 1, "b": 2}
var m3 map[string]int               // nil map，只能读不能写
```

### 读写

```go
m["apple"] = 5
m["banana"] = 3

fmt.Println(m["apple"])     // 5
fmt.Println(m["orange"])   // 0（不存在的 key 返回值类型零值）
```

### 判断 key 是否存在

用双返回值形式：

```go
v, ok := m["orange"]
if ok {
    fmt.Println("存在:", v)
} else {
    fmt.Println("不存在")
}
```

`ok` 为 true 表示 key 存在；为 false 时 `v` 是零值。

### 遍历

map 遍历顺序**随机**（Go 刻意打乱，防止依赖顺序）：

```go
for k, v := range m {
    fmt.Println(k, v)
}
```

只要 key：`for k := range m { ... }`。

### 删除与长度

```go
delete(m, "banana")    // 删除 key
n := len(m)            // 键值对个数
```

### key 的要求

key 必须是**可比较类型**：基本类型、字符串、数组、接口、指针；**不能是切片、map、函数**（不可比较）。value 可以是任意类型。

### 常见嵌套

```go
graph := map[string][]string{
    "a": {"b", "c"},
    "b": {"d"},
}
```

## 核心重点
- map 必须 make 或字面量初始化；nil map 写会 panic
- 读不存在的 key 返回零值，用 `v, ok := m[k]` 判断存在性
- 遍历顺序随机，不要依赖
- `delete(m, k)` 删除；`len(m)` 取大小
- key 必须可比较，切片/map 不能做 key

## 练习/任务
1. 用 map 统计一段英文中每个单词出现次数
2. 写一个程序判断 key "age" 是否存在，分别处理存在与不存在
3. 向 nil map 写入一个键值对，观察 panic
4. 构建 `map[string][]int`，给每个 key 存一组数字

## 打卡清单
- [ ] 会创建、读写、遍历 map
- [ ] 会用 `v, ok` 判断 key 存在
- [ ] 知道 nil map 不能写
- [ ] 理解 map 遍历顺序随机
