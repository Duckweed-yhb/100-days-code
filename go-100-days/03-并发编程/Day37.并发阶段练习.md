# Day 37 · 并发阶段练习

## 今日目标
- 综合运用 goroutine、channel、select、sync、context
- 完成一个带超时与并发限制的小工具
- 排查经典并发坑

## 学习内容

### 本期回顾

| 主题 | 关键能力 |
|---|---|
| goroutine | go 启动，main 会杀子协程 |
| channel | 收发、缓冲、关闭、range |
| select | 多路复用、超时、default |
| 锁 | Mutex、RWMutex、WaitGroup |
| 模式 | 生产者消费者、扇入扇出、Once |
| 高级 | atomic、context |

### 示范：并发抓取多个 URL（带超时）

```go
package main

import (
    "context"
    "fmt"
    "sync"
    "time"
)

func fetch(ctx context.Context, url string) string {
    select {
    case <-time.After(100 * time.Millisecond): // 模拟网络耗时
        return url + " -> 200 OK"
    case <-ctx.Done():
        return url + " -> 取消"
    }
}

func main() {
    urls := []string{"a.com", "b.com", "c.com", "d.com"}
    ctx, cancel := context.WithTimeout(context.Background(), 150*time.Millisecond)
    defer cancel()

    results := make(chan string, len(urls))
    var wg sync.WaitGroup
    for _, u := range urls {
        wg.Add(1)
        go func(u string) {
            defer wg.Done()
            results <- fetch(ctx, u)
        }(u)
    }
    go func() { wg.Wait(); close(results) }()

    for r := range results {
        fmt.Println(r)
    }
}
```

要点：循环变量 `u` 作为参数传给闭包（否则所有 goroutine 共享同一个 u）；context 统一超时；WaitGroup 等完后 close results。

## 核心重点
- 并发三件套：goroutine 干活、channel 通信、WaitGroup/context 同步与取消
- 循环变量要传参进闭包
- 资源关闭由发送方负责
- 永远考虑超时，不然 goroutine 泄漏

## 练习/任务
1. 跑通上面并发抓取例子
2. 改造成：最多同时跑 2 个任务（带缓冲信号量 channel）
3. 写一个程序：并发计算 1~1000 每个数是否为质数，结果汇总
4. （选做）用 errgroup 风格重写，任一任务失败就取消其他

## 今日刷题

### 1. LeetCode 200 岛屿数量
- 思路：DFS/BFS 遍历网格，遇到 '1' 就沉没整片岛屿并计数。
- 代码提示：双重循环，每遇 '1' 计数+1，DFS 把相邻 '1' 改为 '0'。
- Go 要点：方向数组 `[4][2]int{{1,0},{-1,0},{0,1},{0,-1}}`。

### 2. LeetCode 207 课程表（拓扑排序）
- 思路：建图+入度，BFS 剥加入度 0 节点。
- 代码提示：邻接表 + 队列，能修完所有课则无环。
- Go 要点：理解 DAG 拓扑。

## 打卡清单
- [ ] 跑通并发抓取示例
- [ ] 实现并发限制（信号量）
- [ ] 写完岛屿数量
- [ ] 阶段三所有并发概念亲手写过
