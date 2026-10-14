# Day 41 · time时间包

## 今日目标
- 掌握 time.Now 与时间戳
- 学会时间格式化（Go 独特的参考时间）
- 理解 Duration 与定时器

## 学习内容

### 当前时间与时间戳

```go
now := time.Now()
fmt.Println(now)                       // 2026-09-29 10:00:00 +0800 CST
unix := now.Unix()                     // 秒级时间戳
milli := now.UnixMilli()               // 毫秒
```

### 时间格式化：Go 的怪传统

Go 用一个**神奇参考时间** `2006-01-02 15:04:05` 作格式模板，而不是 YYYY/mm/dd：

```go
now.Format("2006-01-02 15:04:05")    // 2026-09-29 10:00:00
now.Format("2006-01-02")              // 2026-09-29
now.Format("01/02/06 3PM")            // 09/29/26 10AM
```

记忆：2006 年 1 月 2 日 3 点 4 分 5 秒。

### 解析时间

```go
t, err := time.Parse("2006-01-02", "2026-09-29")
```

### Duration 时长

```go
d := 2 * time.Second
d2 := 500 * time.Millisecond
time.Sleep(500 * time.Millisecond)
```

Duration 本质是 int64 纳秒，可直接比较和运算。

### 时间运算

```go
future := now.Add(24 * time.Hour)
diff := future.Sub(now)              // 24h
bool := now.Before(future)
```

### 定时器

```go
ticker := time.NewTicker(1 * time.Second)
for t := range ticker.C {
    fmt.Println(t)
}
```

## 核心重点
- `time.Now()` 当前时间，`Unix()` 时间戳
- 格式化参考时间 `2006-01-02 15:04:05`，别用 YYYY
- `time.Parse` 解析字符串
- `time.Duration` 表示时长，可乘除
- Add/Sub/Before/After 比较时间

## 练习/任务
1. 打印当前时间，格式为 `2006-01-02 15:04:05`
2. 计算明天这个时间是几点
3. 把字符串 "2026-01-01" 解析成 time.Time
4. 用 time.Ticker 每秒打印一次，共 5 次后退出

## 今日刷题

### 1. LeetCode 1 两数之和
- 思路：一遍遍历，用 map 存"值→下标"，查 `target - x` 是否已出现。
- 代码提示：`m := map[int]int{}`，`if j, ok := m[target-x]; ok { return []int{j, i} }`。
- Go 要点：map 查找 O(1)。

### 2. LeetCode 9 Palindrome Number（字符串法复习）
- 思路：`s := strconv.Itoa(x)`，双指针。
- 代码提示：对比 `s[i]` 与 `s[len(s)-1-i]`。
- Go 要点：巩固 strconv。

## 打卡清单
- [ ] 会用 Now/Format/Parse
- [ ] 记住参考时间 2006-01-02 15:04:05
- [ ] 会用 Duration 做等待和运算
- [ ] 写完两数之和
