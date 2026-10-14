# Day 28 · 缓冲通道与关闭

## 今日目标
- 掌握缓冲 channel 与无缓冲的区别
- 学会关闭 channel、遍历 channel
- 理解 close 与 ok 模式

## 学习内容

### 缓冲 channel

`make(chan T, 容量)`：通道内部有个队列，不满时发送不阻塞、不空时接收不阻塞。

```go
ch := make(chan int, 2)
ch <- 1        // 不阻塞，队列有 1 个
ch <- 2        // 不阻塞，队列满
// ch <- 3     // 阻塞，直到有人接收

fmt.Println(<-ch)   // 1
fmt.Println(<-ch)   // 2
```

### 关闭 channel

发送方用完后 `close(ch)` 通知接收方"不会再有数据了"：

```go
func worker(ch chan int) {
    for i := 0; i < 5; i++ {
        ch <- i
    }
    close(ch)
}

func main() {
    ch := make(chan int, 5)
    go worker(ch)
    for n := range ch {     // 自动接收直到 channel 关闭
        fmt.Println(n)
    }
}
```

`for range ch` 会一直收，channel 关闭且取空后自动结束循环。

### 接收时判断是否关闭

```go
v, ok := <-ch
// ok=false 表示 channel 已关闭且无数据
```

### 规则

- 应该由**发送方**关闭 channel，不要由接收方关闭——向已关闭 channel 发送会 panic。
- 关闭后还能接收缓冲里剩余的数据，取完后得到零值且 ok=false。
- 不关闭也没事，channel 会被 GC 回收（不是文件句柄，不用刻意关）。

## 核心重点
- 缓冲 channel 满才阻塞发送、空才阻塞接收
- `close(ch)` 由发送方调用
- `for n := range ch` 自动取到关闭
- `v, ok := <-ch` 判断是否关闭
- 不要向已关闭 channel 发送

## 练习/任务
1. 创建容量 3 的缓冲 channel，连续发 3 个不阻塞，发第 4 个观察阻塞
2. 写一个 goroutine 发 10 个数后 close，main 用 range 接收
3. 接收一个已关闭 channel，观察 ok 为 false
4. 写一个程序：发送方发完数据 close，接收方 range 打印

## 今日刷题

### 1. LeetCode 88 合并两个有序数组
- 思路：从后往前双指针，避免额外数组；nums1 后部留足空间。
- 代码提示：`i, j, k := m-1, n-1, m+n-1`，谁大谁放。
- Go 要点：直接原地写 nums1。

### 2. LeetCode 283 移动零
- 思路：双指针，慢指针 j 写下非零，末尾补零。
- 代码提示：`for _, v := range nums { if v!=0 { nums[j]=v; j++ } }`，再把 j 之后填 0。
- Go 要点：一次遍历把非零前移，第二次填零。

## 打卡清单
- [ ] 会用缓冲 channel
- [ ] 会关闭 channel 并用 range 遍历
- [ ] 理解 ok 判断关闭
- [ ] 写完合并数组与移动零
