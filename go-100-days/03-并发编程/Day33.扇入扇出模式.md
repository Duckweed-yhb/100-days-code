# Day 33 · 扇入扇出模式

## 今日目标
- 理解扇出（fan-out）分发任务到多个 worker
- 理解扇入（fan-in）汇总多个结果
- 学会用 WaitGroup 汇总结果

## 学习内容

### 扇出 fan-out

把任务分给多个 goroutine 并行处理：

```go
func fanOut(nums []int, workers int) <-chan int {
    out := make(chan int)
    var wg sync.WaitGroup
    for _, n := range nums {
        wg.Add(1)
        go func(x int) {
            defer wg.Done()
            out <- x * x       // 每个 worker 算平方
        }(n)
    }
    go func() {
        wg.Wait()
        close(out)
    }()
    return out
}
```

### 扇入 fan-in

把多个 channel 合并成一个：

```go
func fanIn(channels ...<-chan int) <-chan int {
    out := make(chan int)
    var wg sync.WaitGroup
    for _, ch := range channels {
        wg.Add(1)
        go func(c <-chan int) {
            defer wg.Done()
            for v := range c {
                out <- v
            }
        }(ch)
    }
    go func() { wg.Wait(); close(out) }()
    return out
}
```

### 组合使用

```go
func main() {
    c1 := fanOut([]int{1, 2, 3}, 3)
    c2 := fanOut([]int{4, 5, 6}, 3)
    merged := fanIn(c1, c2)
    sum := 0
    for v := range merged {
        sum += v
    }
    fmt.Println(sum)   // 1+4+9+16+25+36 = 91
}
```

### 与 errgroup 预告

实际项目里常用 `golang.org/x/sync/errgroup`，它能等待一组 goroutine 并返回第一个错误，Day36 讲 context 后会用到。

## 核心重点
- fan-out：任务分给多个 goroutine 并行
- fan-in：多个 channel 合并成一个
- 用 WaitGroup 等所有 worker 结束后 close 输出 channel
- 关闭 channel 的 goroutine 要等发送方都结束
- 适合 CPU 密集型并行计算

## 练习/任务
1. 跑通 fan-out/fan-in 例子
2. 给一组数字并行求平方，汇总求和
3. 写一个 fan-in 合并 3 个字符串 channel
4. （选做）限制并发数：同时最多 5 个 worker

## 今日刷题

### 1. LeetCode 101 对称二叉树
- 思路：递归比较左右子树是否镜像。
- 代码提示：`isMirror(l, r)`：都空 true；一空 false；值相等且 `isMirror(l.Left,r.Right) && isMirror(l.Right,r.Left)`。
- Go 要点：成对比较。

### 2. LeetCode 226 翻转二叉树
- 思路：递归交换左右子树。
- 代码提示：`root.Left, root.Right = invertTree(root.Right), invertTree(root.Left)`。
- Go 要点：一行交换技巧。

## 打卡清单
- [ ] 会写 fan-out 分发任务
- [ ] 会写 fan-in 合并 channel
- [ ] 理解 WaitGroup 配合 close
- [ ] 写完对称与翻转二叉树
