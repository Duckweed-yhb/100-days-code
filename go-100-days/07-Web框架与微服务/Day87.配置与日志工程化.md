# Day 87 · 配置与日志工程化

## 今日目标
- 学会用环境变量与配置文件管理配置
- 理解 12-Factor App 理念
- 生产级日志方案

## 学习内容

### 配置管理

不要把配置写死在代码里。常用方案：

1. 环境变量（12-Factor 推荐）
2. 配置文件（yaml/json）

### 环境变量

```go
port := os.Getenv("PORT")
if port == "" {
    port = "8080"
}
```

### 用 viper 管理配置

```powershell
go get github.com/spf13/viper
```

```go
viper.SetConfigName("config")
viper.AddConfigPath(".")
viper.ReadInConfig()
port := viper.GetInt("server.port")
```

### 12-Factor App

- 配置存在环境里，不写死代码
- 日志当作事件流，不写文件
- 进程无状态

### 结构化日志

生产用 `log/slog`：

```go
slog.Info("用户登录", "user_id", 42, "ip", "1.2.3.4")
```

输出 JSON，方便 ELK/Loki 采集。

### 优雅关闭

```go
srv := &http.Server{Addr: ":8080"}
go srv.ListenAndServe()

quit := make(chan os.Signal, 1)
signal.Notify(quit, os.Interrupt)
<-quit

ctx, cancel := context.WithTimeout(context.Background(), 5*time.Second)
defer cancel()
srv.Shutdown(ctx)
```

## 核心重点
- 配置用环境变量或配置文件，不写死
- viper 管理多环境配置
- slog 结构化日志
- 优雅关闭：收到信号后 Shutdown
- 12-Factor：配置外置、日志流、无状态

## 练习/任务
1. 用环境变量设置端口
2. 用 viper 读一个 config.yaml
3. 用 slog 替换 fmt.Println
4. 给服务加优雅关闭

## 今日刷题

### 1. LeetCode 20 有效的括号（复习）
- 思路：栈。
- 代码提示：map。
- Go 要点：栈。

### 2. LeetCode 21 合并两个有序链表（复习）
- 思路：哑节点。
- 代码提示：谁小接谁。
- Go 要点：链表。

## 打卡清单
- [ ] 会用环境变量与 viper
- [ ] 会用 slog
- [ ] 会优雅关闭
- [ ] 复习括号与链表
