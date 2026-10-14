# Day 27 · channel通道基础

## 今日目标
- 理解 channel 是 goroutine 间通信管道
- 学会无缓冲 channel 的收发
- 用 channel 替代 Sleep 做同步

## 学习内容

### channel 是什么

channel 是带类型的管道，用来在 goroutine 之间传值。"不要用共享内存通信，而要用通信共享内存。"

### 创建与收发

```go
ch := make(chan string)   // 无缓冲 channel

go func() {
    ch <- "hello"         // 发送（写入管道）
}()

msg := <-ch              // 接收（阻塞等待）
fmt.Println(msg)
```

`ch <- v` 发送；`v := <-ch` 接收。

### 无缓冲 channel 会阻塞

无缓冲 channel 的发送方会**阻塞**，直到有接收方就位；接收方也阻塞直到有数据。它天然实现了同步：上面例子里 main 必须等 goroutine 把 "hello" 发出来才继续，不用 Sleep。

### 用 channel 等待 goroutine

```go
func worker(done chan bool) {
    fmt.Println("working...")
    done <- true
}

func main() {
    done := make(chan bool)
    go worker(done)
    <-done          // 阻塞直到 worker 发信号
    fmt.Println("done")
}
```

### 双向 vs 单向

函数参数可以声明为单向 channel：

```go
func sendOnly(ch chan<- string) { ch <- "hi" }   // 只能发
func recvOnly(ch <-chan string) { <-ch }         // 只能收
```

这是编译期约束，防止误用。

## 核心重点
- `make(chan T)` 创建管道，类型 T 是传递数据类型
- `ch <- v` 发送，`v := <-ch` 接收
- 无缓冲 channel 收发必须碰头，天然同步
- 用 channel 等待 goroutine 结束比 Sleep 靠谱
- 单向 channel `chan<- T` / `<-chan T` 用于接口约束

## 练习/任务
1. 启动一个 goroutine 计算平方，结果通过 channel 发回 main
2. 写两个 goroutine，一个发数字一个收数字，用 channel 传 5 个数
3. 把 Day26 的 say 例子改成用 channel 等待结束
4. 写一个只发送 channel 参数函数，验证外部不能用它接收

## 今日刷题

### 1. LeetCode 26 删除有序数组重复项
- 思路：双指针，慢指针 j 指向去重后位置，快指针 i 扫描；`nums[i] != nums[j-1]` 时复制过去。
- 代码提示：`j := 1`，从 i=1 开始遍历。
- Go 要点：直接修改切片，返回新长度。

### 2. LeetCode 27 移除元素
- 思路：双指针覆盖；或快慢指针把不等于 val 的元素前移。
- 代码提示：`k := 0; for _, v := range nums { if v != val { nums[k]=v; k++ } }`。
- Go 要点：range 遍历配合下标写入。

## 打卡清单
- [ ] 会创建、发送、接收 channel
- [ ] 理解无缓冲 channel 的阻塞语义
- [ ] 会用 channel 做 goroutine 同步
- [ ] 写完两道数组双指针题
