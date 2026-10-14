# Day 36 · context上下文

## 今日目标
- 理解 context 用于跨 goroutine 传递取消信号与请求元数据
- 掌握 WithCancel / WithTimeout / WithValue
- 学会在 HTTP、并发任务中正确取消

## 学习内容

### context 是什么

`context.Context` 是 Go 官方的请求级上下文，用来在 goroutine 树之间传递：取消信号、超时、截止时间、请求数据。

### WithCancel：手动取消

```go
ctx, cancel := context.WithCancel(context.Background())
defer cancel()

go func() {
    select {
    case <-time.After(5 * time.Second):
        fmt.Println("做完了")
    case <-ctx.Done():
        fmt.Println("被取消:", ctx.Err())
    }
}()

time.Sleep(100 * time.Millisecond)
cancel()       // 主动取消
```

### WithTimeout：超时自动取消

```go
ctx, cancel := context.WithTimeout(context.Background(), 2*time.Second)
defer cancel()

// 传给下游，下游监听 ctx.Done()
```

请求超时、数据库查询超时都用它。

### WithValue：传请求级数据

```go
ctx := context.WithValue(context.Background(), TraceIDKey{}, "abc123")
// 下游 ctx.Value(TraceIDKey{}) 取出
```

只传请求域数据（trace id、用户身份），不要用它传业务参数。

### 派生 context

context 形成树：父取消，所有派生的子 context 都取消。这是它强大的地方——一个请求超时，整条调用链的 goroutine 都被通知退出。

## 核心重点
- `context.Context` 传递取消、超时、请求数据
- `context.WithCancel` 手动取消
- `context.WithTimeout` 超时自动取消
- `ctx.Done()` 是 channel，取消时关闭
- 父 context 取消会级联取消所有子 context
- 始终 defer cancel() 防止泄漏

## 练习/任务
1. 写一个 goroutine 监听 ctx.Done()，1 秒后 cancel
2. 用 WithTimeout 模拟一个 2 秒超时的任务
3. 用 WithValue 传一个 trace id 到子 goroutine 并打印
4. （选做）启动 3 个派生 goroutine，cancel 父 context 看它们一起退出

## 今日刷题

### 1. LeetCode 98 验证二叉搜索树
- 思路：递归传上下界，每个节点值必须在 (min, max) 区间。
- 代码提示：`helper(node, min, max int64)`，逐层收紧边界。
- Go 要点：用 int64 防 int 边界溢出。

### 2. LeetCode 230 BST 第 K 小元素
- 思路：中序遍历是升序，数到第 K 个返回。
- 代码提示：递归中序，用计数器递减，到 0 记录答案。
- Go 要点：利用 BST 中序有序性质。

## 打卡清单
- [ ] 会用 WithCancel/WithTimeout
- [ ] 理解 ctx.Done() 与级联取消
- [ ] 会用 WithValue 传请求数据
- [ ] 写完 BST 验证与第 K 小题
