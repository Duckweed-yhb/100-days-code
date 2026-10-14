# Day 38 · 标准库概览与fmt

## 今日目标
- 了解 Go 标准库全貌，知道常用包在哪
- 熟练使用 fmt 的打印与格式化
- 掌握 verbs 占位符

## 学习内容

### 标准库地图

| 包 | 用途 |
|---|---|
| fmt | 输入输出格式化 |
| strings / strconv | 字符串处理 / 类型转换 |
| time | 时间日期 |
| os | 文件、命令行参数、环境 |
| io / ioutil | 输入输出抽象 |
| encoding/json | JSON |
| net/http | HTTP 服务与客户端 |
| database/sql | 数据库 |
| testing | 测试 |
| flag | 命令行参数 |

Go 的底气就是标准库强大，先别着急上第三方库。

### fmt 常用函数

```go
fmt.Print("不换行")
fmt.Println("自动换行")
fmt.Printf("整数 %d 字符串 %s\n", 42, "hi")
```

### 常用占位符 verbs

| verb | 用途 | 示例输出 |
|---|---|---|
| `%d` | 整数 | 42 |
| `%s` | 字符串 | hi |
| `%f` | 浮点 | 3.140000 |
| `%.2f` | 两位小数 | 3.14 |
| `%v` | 默认格式 | 万能 |
| `%+v` | 结构体带字段名 | {Name:Bob} |
| `%#v` | Go 语法表示 | main.User{Name:"Bob"} |
| `%T` | 类型 | main.User |
| `%t` | 布尔 | true |
| `%%` | 百分号 | % |

### Sprintf：拼字符串

```go
name := "Bob"
msg := fmt.Sprintf("你好, %s", name)
```

### 调试技巧

```go
fmt.Printf("%#v\n", someStruct)   // 打印完整结构，调试神器
```

## 核心重点
- 标准库覆盖 80% 日常需求，先学标准库
- Println/Printf/Sprintf 三者分工
- `%v` 万能、`%+v` 看字段、`%#v` 看完整结构
- `%T` 打印类型
- Sprintf 用来拼字符串不落盘

## 练习/任务
1. 打印一个结构体，分别用 %v、%+v、%#v 看区别
2. 用 Sprintf 拼接"用户 Bob 今年 20 岁"
3. 用 %.2f 打印 3.14159 保留两位
4. 故意用错 verb（如给字符串用 %d），观察警告

## 今日刷题

### 1. LeetCode 7 整数反转
- 思路：不断取末位 `x % 10`，拼到反转数 `rev = rev*10 + digit`。
- 代码提示：注意溢出判断（Go int 64 位不易溢出，但仍按题意判断 32 位范围）。
- Go 要点：负数取余在 Go 里保留符号。

### 2. LeetCode 9 回文数（字符串法）
- 思路：转字符串双指针，或 Day26 的数学法。
- 代码提示：`s := strconv.Itoa(x)`，左右指针向中间。
- Go 要点：练习 fmt 与 strconv。

## 打卡清单
- [ ] 会用 Printf 常用 verbs
- [ ] 会用 Sprintf 拼字符串
- [ ] 知道 %+v 与 %#v 的调试用途
- [ ] 写完整数反转与回文数
