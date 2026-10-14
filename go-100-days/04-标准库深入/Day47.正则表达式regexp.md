# Day 47 · 正则表达式regexp

## 今日目标
- 学会编译正则、匹配、提取
- 掌握常用正则元字符
- 知道正则在 Go 里的性能与坑

## 学习内容

### 编译正则

```go
import "regexp"

re := regexp.MustCompile(`\d+`)    //  MustCompile 失败 panic；用 Compile 返回 error
```

用反引号写正则，避免转义地狱。

### 常用方法

```go
re.MatchString("abc 123 def")       // true
re.FindString("abc 123 def")        // "123"，第一个匹配
re.FindAllString("a1 b2 c3", -1)    // [1 2 3]，全部
re.ReplaceAllString("abc", `[a-z]`, "X")  // "XXX"
```

### 捕获分组

```go
re := regexp.MustCompile(`(\w+)@(\w+)\.com`)
m := re.FindStringSubmatch("bob@example.com")
// m[0]="bob@example.com" m[1]="bob" m[2]="example"
```

### 常用元字符

| 字符 | 含义 |
|---|---|
| `.` | 任意字符 |
| `\d` | 数字，`\D` 非数字 |
| `\w` | 单词字符 |
| `* + ?` | 零多/一多/零一 |
| `{n,m}` | n 到 m 次 |
| `^ $` | 开头结尾 |
| `[abc]` | 字符集 |

### 注意

- 正则比手写字符串匹配慢，简单判断优先用 strings 包。
- 编译一次复用，别在循环里 MustCompile。

## 核心重点
- `regexp.MustCompile` 编译正则
- MatchString/FindString/FindAllString/ReplaceAllString
- 分组用 FindStringSubmatch，m[0] 是整体
- 正则编译一次复用
- 简单匹配优先 strings，复杂模式才用正则

## 练习/任务
1. 写正则提取一段文本里所有手机号（11 位数字）
2. 用正则把 "2026-09-29" 中的年/月/日分别提取
3. 把一段文本里所有邮箱替换为 `[隐藏]`
4. 判断一个字符串是否是合法的 IPv4 格式

## 今日刷题

### 1. LeetCode 28 找出字符串中第一个匹配项下标
- 思路：暴力匹配或 strings.Index。
- 代码提示：直接 `strings.Index(haystack, needle)`。
- Go 要点：标准库解法。

### 2. LeetCode 58 最后一个单词长度（复习）
- 思路：Split 后取最后非空。
- 代码提示：`strings.Fields(s)` 按空白切。
- Go 要点：strings.Fields。

## 打卡清单
- [ ] 会编译和使用正则
- [ ] 会提取分组
- [ ] 知道正则与 strings 的取舍
- [ ] 写完字符串匹配题
