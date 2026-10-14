# Day 06 · 条件分支if与switch

## 今日目标
- 熟练使用 if / else if / else
- 掌握 if 的初始化语句
- 学会 Go 风格的 switch（自动 break、可无表达式、可做类型分支）

## 学习内容

### if 条件

```go
if x > 10 {
    fmt.Println("big")
} else if x > 0 {
    fmt.Println("small positive")
} else {
    fmt.Println("zero or negative")
}
```

规则：条件**不写括号**；左花括号必须与 `if` 同行。

### if 的初始化语句

Go 推荐在 if 里先初始化再判断，常用于 error：

```go
if err := doSomething(); err != nil {
    fmt.Println("出错:", err)
    return err          // err 只在 if/else 块内可见
}
```

### switch 分支

Go 的 switch **默认每个 case 自动 break**，不需要手写；想贯穿用 `fallthrough`：

```go
switch day := 3; day {
case 1, 2, 3, 4, 5:
    fmt.Println("工作日")
case 6, 7:
    fmt.Println("周末")
default:
    fmt.Println("未知")
}
```

### 无表达式 switch（替代 if-else 链）

```go
score := 85
switch {
case score >= 90:
    fmt.Println("优")
case score >= 80:
    fmt.Println("良")
case score >= 60:
    fmt.Println("及格")
default:
    fmt.Println("不及格")
}
```

这种写法比一长串 `else if` 更清晰。

### （预告）类型 switch

Day19 会学：`switch v := x.(type)` 可以按接口的具体类型分支，现在先知道有这东西。

## 核心重点
- if 条件不用括号，左花括号同行
- `if err := f(); err != nil { ... }` 是 Go 处理错误的惯用写法
- switch 每个 case 默认自动 break，多值用逗号分隔
- 无表达式 switch 可当 if-else 链用，更可读
- `default` 分支可选

## 练习/任务
1. 写程序根据分数（0~100）打印优/良/及格/不及格，用无表达式 switch
2. 用 if 初始化语句写：打开一个不存在的文件，把 err 打印出来
3. 写一个星期几的程序，工作日 case 用逗号合并多个值
4. 故意让某个 case 贯穿到下一个，体会 fallthrough

## 打卡清单
- [ ] 会写 if / else if / else
- [ ] 会用 `if err := ...; err != nil` 惯用法
- [ ] 知道 Go switch 默认不贯穿
- [ ] 会用无表达式 switch 替代长 if-else
