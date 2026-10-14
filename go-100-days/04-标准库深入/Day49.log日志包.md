# Day 49 · log日志包

## 今日目标
- 学会使用标准 log 包
- 理解日志级别与输出位置
- 知道何时该上结构化日志库

## 学习内容

### 基本用法

```go
import "log"

log.Println("服务器启动")
log.Printf("监听端口 %d", 8080)
log.Fatalln("致命错误")    // 打印后 os.Exit(1)
```

`log` 默认输出到 stderr，自动带时间戳。

### 输出到文件

```go
f, _ := os.OpenFile("app.log", os.O_CREATE|os.O_WRONLY|os.O_APPEND, 0644)
log.SetOutput(f)
```

### 自定义前缀与标志

```go
log.SetPrefix("[APP] ")
log.SetFlags(log.Ldate | log.Ltime | log.Lshortfile)
// [APP] 2026/09/29 10:00:00 main.go:10: 消息
```

### log.Fatal 与 Panic 的区别

- `log.Fatal`：打印后退出程序（os.Exit(1)），defer 不执行。
- `log.Panic`：打印后 panic，能被 recover。

### 结构化日志

标准 log 不支持 JSON 字段，生产环境常用 `log/slog`（Go 1.21+）：

```go
slog.Info("用户登录", "user", "Bob", "age", 20)
// 输出带结构化字段
```

## 核心重点
- log.Println/Printf/Fatalln
- SetOutput 改输出位置，SetPrefix/SetFlags 改格式
- Fatal 直接退出，Panic 可 recover
- 生产环境用 log/slog 结构化日志
- 不要用 fmt.Println 打日志

## 练习/任务
1. 用 log 打印启动信息和一条错误
2. 把日志输出到文件
3. 给日志加前缀和文件名行号
4. 试一下 slog.Info 带字段输出

## 今日刷题

### 1. LeetCode 20 有效的括号（复习）
- 思路：栈。
- 代码提示：map 配对。
- Go 要点：栈实现。

### 2. LeetCode 21 合并两个有序链表（复习）
- 思路：哑节点。
- 代码提示：谁小接谁。
- Go 要点：链表操作。

## 打卡清单
- [ ] 会用 log 包打日志
- [ ] 会配置输出位置和格式
- [ ] 知道 Fatal 与 Panic 区别
- [ ] 复习括号与链表题
