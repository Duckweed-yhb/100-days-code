# Day 29 · select多路复用

## 今日目标
- 掌握 select 同时等待多个 channel
- 学会 default 分支做非阻塞操作
- 理解超时控制的常见写法

## 学习内容

### select：同时等多个 channel

`select` 像 switch，但每个 case 是一个 channel 操作：

```go
select {
case msg := <-ch1:
    fmt.Println("来自 ch1:", msg)
case msg := <-ch2:
    fmt.Println("来自 ch2:", msg)
}
```

多个 channel 都就绪时，**Go 随机选一个**执行；都没就绪时阻塞。

### 超时控制

配合 `time.After` 给操作加超时：

```go
select {
case res := <-work:
    fmt.Println("结果:", res)
case <-time.After(2 * time.Second):
    fmt.Println("超时")
}
```

这是 Go 里做请求超时的标配写法。

### default 非阻塞

```go
select {
case msg := <-ch:
    fmt.Println("收到:", msg)
default:
    fmt.Println("没数据，先干别的")
}
```

没有 default 时 select 阻塞；有 default 时立即走 default，实现非阻塞。

### 定时器与打点器

```go
tick := time.Tick(500 * time.Millisecond)
for t := range tick {
    fmt.Println("滴答", t)
}
```

## 核心重点
- `select` 同时等待多个 channel，就绪一个就执行
- 多分支就绪时随机选
- `time.After` 做超时
- `default` 实现非阻塞收发
- 避免在 for 里空转 select 加 default，会烧 CPU

## 练习/任务
1. 启动两个 goroutine，分别向两个 channel 发数据，用 select 接收
2. 给一个慢操作加 1 秒超时，观察超时分支
3. 写一个非阻塞发送：channel 满就打印"忙"而不阻塞
4. 用 time.Tick 每秒打印一次，5 秒后退出

## 今日刷题

### 1. LeetCode 21 合并两个有序链表
- 思路：哑节点 + 双指针，谁小接谁。
- 代码提示：`dummy := &ListNode{}; cur := dummy`，循环比较 l1.Val、l2.Val。
- Go 要点：Go 用指针表示节点，注意 nil 判断。

### 2. LeetCode 94 二叉树中序遍历
- 思路：递归或栈。左→根→右。
- 代码提示：递归 `inorder(root.Left); append; inorder(root.Right)`。
- Go 要点：切片 append 收集结果。

## 打卡清单
- [ ] 会用 select 同时等多个 channel
- [ ] 会用 time.After 做超时
- [ ] 会写 default 非阻塞
- [ ] 写完合并链表与中序遍历
