# Day 30 · 互斥锁Mutex

## 今日目标
- 理解为什么需要互斥锁（数据竞争）
- 掌握 sync.Mutex 的 Lock/Unlock
- 学会用 defer Unlock

## 学习内容

### 数据竞争

多个 goroutine 同时读写同一变量会产生数据竞争，结果不确定：

```go
var counter = 0

func worker() {
    for i := 0; i < 1000; i++ {
        counter++       // 不是原子操作！
    }
}

func main() {
    for i := 0; i < 10; i++ {
        go worker()
    }
    time.Sleep(time.Second)
    fmt.Println(counter)   // 期望 10000，实际常少
}
```

`counter++` 看似一行，实际是"读-改-写"三步，多 goroutine 交错就丢更新。

### sync.Mutex 加锁

```go
var mu sync.Mutex
var counter = 0

func worker() {
    for i := 0; i < 1000; i++ {
        mu.Lock()
        counter++
        mu.Unlock()
    }
}
```

同一时刻只有一个 goroutine 能持有锁。

### defer Unlock

```go
mu.Lock()
defer mu.Unlock()
// ... 临界区
```

即使临界区有多条 return，defer 也保证解锁。

### 检查数据竞争

Go 自带数据竞争检测器：

```powershell
go run -race main.go
```

开启后跑上面的反例，会打印 data race 警告。

## 核心重点
- 共享可变数据必须加锁
- `mu.Lock()` / `mu.Unlock()` 配对
- 用 defer Unlock 防止忘解锁
- `go run -race` 检测数据竞争
- 锁粒度尽量小，只保护共享数据段

## 练习/任务
1. 跑上面的计数器反例，用 -race 看警告
2. 加 Mutex 让结果正确为 10000
3. 写一个并发安全的账户：存取款都加锁
4. 故意忘 Unlock，观察死锁（程序卡住）

## 今日刷题

### 1. LeetCode 206 反转链表
- 思路：三指针 prev/cur/next 逐个反转。
- 代码提示：`for cur != nil { nxt := cur.Next; cur.Next = prev; prev = cur; cur = nxt }`。
- Go 要点：注意先存 next 再改指向，别断链。

### 2. LeetCode 141 环形链表
- 思路：快慢指针，快的追上慢的即有环。
- 代码提示：`slow, fast := head, head; for fast != nil && fast.Next != nil { slow=slow.Next; fast=fast.Next.Next }`。
- Go 要点：边界空链表。

## 打卡清单
- [ ] 理解数据竞争的成因
- [ ] 会用 Mutex 保护共享变量
- [ ] 会用 -race 检测竞争
- [ ] 写完反转与环形链表
