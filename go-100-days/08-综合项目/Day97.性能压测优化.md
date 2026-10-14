# Day 97 · 性能压测优化

## 今日目标
- 用基准测试测关键路径
- 用压测工具看吞吐
- 找到并优化瓶颈

## 学习内容

### 写基准测试

```go
func BenchmarkCreateTodo(b *testing.B) {
    db := setupBenchDB()
    for i := 0; i < b.N; i++ {
        Create(db, 1, "test")
    }
}
```

```powershell
go test -bench=. -benchmem
```

### 压测工具

第三方工具 `wrk` 或 `ab`：

```powershell
wrk -t4 -c100 -d10s http://localhost:8080/todos
```

含义：4 线程、100 并发、压 10 秒。

### 常见优化点

| 优化 | 说明 |
|---|---|
| 数据库连接池 | SetMaxOpenConns |
| 避免重复序列化 | 用 Encoder |
| 加缓存 | 热点数据放内存 |
| 减少日志开销 | 异步日志 |

### pprof

Go 内置性能分析：

```go
import _ "net/http/pprof"
// 访问 /debug/pprof/
```

## 核心重点
- 基准测试测函数耗时
- wrk 压测 HTTP 吞吐
- 先测量再优化，别凭感觉
- 数据库连接池是常见瓶颈
- pprof 定位热点

## 练习/任务
1. 给 Create 写基准测试
2. 用 wrk 压测 /todos
3. 调整数据库连接池参数看变化
4. 记录优化前后的 QPS

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
- [ ] 会写基准测试
- [ ] 会用 wrk 压测
- [ ] 找到一个瓶颈并优化
- [ ] 复习括号与链表
