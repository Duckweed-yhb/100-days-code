# Day 39 · 字符串处理strings

## 今日目标
- 熟练使用 strings 包常用函数
- 掌握字符串拼接性能优化 strings.Builder
- 学会 strings.Split / Join / Contains / Trim

## 学习内容

### 常用查询与判断

```go
strings.Contains("hello", "ell")     // true
strings.HasPrefix("hello", "he")    // true
strings.HasSuffix("hello", "lo")    // true
strings.Count("banana", "a")        // 3
strings.Index("hello", "l")         // 2
```

### 大小写与去除

```go
strings.ToUpper("hi")        // "HI"
strings.ToLower("HI")        // "hi"
strings.TrimSpace("  hi  ")  // "hi"
strings.TrimLeft("...hi", ".")  // "hi"
```

### 分割与拼接

```go
parts := strings.Split("a,b,c", ",")   // [a b c]
strings.Join(parts, "-")              // "a-b-c"
```

### 替换与重复

```go
strings.Replace("foo", "o", "0", -1)   // "f00"，-1 替换全部
strings.Repeat("ab", 3)                // "ababab"
```

### 高性能拼接：strings.Builder

循环里用 `+` 拼字符串会反复分配，大量拼接用 Builder：

```go
var b strings.Builder
for _, w := range []string{"a", "b", "c"} {
    b.WriteString(w)
}
result := b.String()
```

### Replacer：多组替换

```go
r := strings.NewReplacer("<", "&lt;", ">", "&gt;")
r.Replace("<div>")   // "&lt;div&gt;"
```

## 核心重点
- Contains/HasPrefix/HasSuffix/Index/Count 是高频查询
- Split 切分、Join 拼接
- TrimSpace 去首尾空白
- 大量字符串拼接用 strings.Builder
- Replace 第四参数 -1 表示全部替换

## 练习/任务
1. 判断一个邮箱字符串是否以 "@" 开头/结尾合理
2. 把 "a,b,,c," 用 Split 切分并去掉空串
3. 用 Builder 拼接 1000 次 "hello"，对比用 + 拼接
4. 写一个函数把 "hello world go" 每个单词首字母大写

## 今日刷题

### 1. LeetCode 58 最后一个单词长度
- 思路：TrimSpace 后从右找第一个空格。
- 代码提示：`strings.TrimSpace(s)`，再反向找。
- Go 要点：熟练用 strings 函数。

### 2. LeetCode 14 最长公共前缀（strings 法）
- 思路：以第一个串为基准，逐字符检查其他串是否以其前缀开头。
- 代码提示：`strings.HasPrefix(strs[i], prefix)`。
- Go 要点：用标准库简化实现。

## 打卡清单
- [ ] 会用 strings 常用查询函数
- [ ] 会 Split/Join/Trim/Replace
- [ ] 会用 Builder 高效拼接
- [ ] 写完最后一个单词与公共前缀
