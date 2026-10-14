# Day 31 · 读写锁与WaitGroup

## 今日目标
- 掌握 sync.RWMutex 读多写少场景
- 学会 sync.WaitGroup 等待一组 goroutine
- 理解何时用锁、何时用 channel

## 学习内容

### sync.WaitGroup

等待 N 个 goroutine 都完成，比 channel 计数更直接：

```go
var wg sync.WaitGroup

for i := 0; i < 5; i++ {
    wg.Add(1)            // 启动前加计数
    go func(n int) {
        defer wg.Done()  // 结束时减计数
        fmt.Println("worker", n)
    }(i)
}

wg.Wait()               // 阻塞直到计数归零
fmt.Println("全部完成")
```

注意：`wg.Add` 必须在 go 语句之前；循环变量要传入闭包（经典坑）。

### sync.RWMutex

读多写少场景：读锁可共享，写锁独占。

```go
var mu sync.RWMutex
var cache = map[string]string{}

func Read(k string) string {
    mu.RLock()
    defer mu.RUnlock()
    return cache[k]
}

func Write(k, v string) {
    mu.Lock()
    defer mu.Unlock()
    cache[k] = v
}
```

多个读者可同时进；一旦有写者，读写都阻塞。

### 锁 vs channel

| 场景 | 选择 |
|---|---|
| 保护一段共享数据 | Mutex/RWMutex |
| 传递消息、协调流程 | channel |
| 等待一组任务结束 | WaitGroup |

Go 格言："通过通信共享内存，而不是通过共享内存通信"——但实际工程里共享状态用锁也完全正确。

## 核心重点
- WaitGroup：Add 加计数、Done 减、Wait 等
- Add 必须在 go 之前；循环变量传参
- RWMutex 读共享、写独占，适合读多写少
- 锁保护数据，channel 传递消息
- 别用 WaitGroup 传数据，它只做计数

## 练习/任务
1. 用 WaitGroup 等 10 个 goroutine 全部跑完
2. 写一个并发安全的缓存 map，读用 RLock、写用 Lock
3. 故意在 go 内部才 Add，观察计数不对的 bug
4. 对比：用 channel 和 WaitGroup 两种方式等任务结束

## 今日刷题

### 1. LeetCode 144 二叉树前序遍历
- 思路：根→左→右，递归或栈。
- 代码提示：递归先 append 当前节点值，再左右。
- Go 要点：切片收集结果。

### 2. LeetCode 145 二叉树后序遍历
- 思路：左→右→根。
- 代码提示：递归顺序与前序调换即可。
- Go 要点：与中序/前序对照练习三种遍历。

## 打卡清单
- [ ] 会用 WaitGroup 等一组 goroutine
- [ ] 会用 RWMutex 优化读多写少
- [ ] 知道锁与 channel 各自适用场景
- [ ] 写完二叉树前/后序遍历
