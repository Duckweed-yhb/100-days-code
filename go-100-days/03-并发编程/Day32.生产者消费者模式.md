# Day 32 · 生产者消费者模式

## 今日目标
- 掌握经典并发模式：生产者-消费者
- 学会用 channel 解耦生产与消费速度
- 理解带缓冲 channel 做队列

## 学习内容

### 模式结构

多个生产者往 channel 塞数据，多个消费者从 channel 取数据处理，channel 作为缓冲队列解耦两者速度。

```go
package main

import (
    "fmt"
    "time"
)

func producer(id int, out chan<- int) {
    for i := 0; i < 3; i++ {
        out <- id*100 + i
        time.Sleep(10 * time.Millisecond)
    }
}

func consumer(id int, in <-chan int, done chan<- bool) {
    for n := range in {
        fmt.Printf("消费者 %d 处理 %d\n", id, n)
    }
    done <- true
}

func main() {
    jobs := make(chan int, 10)

    // 2 个生产者
    for p := 1; p <= 2; p++ {
        go producer(p, jobs)
    }

    // 2 个消费者
    done := make(chan bool, 2)
    for c := 1; c <= 2; c++ {
        go consumer(c, jobs, done)
    }

    // 等生产者发完，再关闭 jobs
    time.Sleep(500 * time.Millisecond)
    close(jobs)

    <-done
    <-done
    fmt.Println("全部处理完")
}
```

### 要点

- 缓冲 channel 容量就是队列长度，平衡生产与消费速度差。
- 生产完必须 `close(jobs)`，否则消费者 range 永远阻塞。
- 多个消费者天然并行处理，提高吞吐。

## 核心重点
- 生产者发 channel，消费者收 channel
- 缓冲 channel 做队列
- 生产结束后 close，消费者 range 自动退出
- 多消费者并行提升吞吐
- 注意发送方负责关闭

## 练习/任务
1. 跑通上面的例子，改成 3 个生产者 3 个消费者
2. 把 jobs 容量改成 1，观察吞吐变化
3. 写一个任务：从 1 开始生产数字，消费者计算平方
4. （选做）用 WaitGroup 替代 done channel 等消费者结束

## 今日刷题

### 1. LeetCode 104 二叉树最大深度
- 思路：递归 max(左高, 右高)+1；或层序遍历数层数。
- 代码提示：`if root == nil { return 0 }; return 1 + max(maxDepth(root.Left), maxDepth(root.Right))`。
- Go 要点：Go 1.21 内置 max/min 可直接用。

### 2. LeetCode 110 平衡二叉树
- 思路：自底向上，返回高度，不平衡时返回 -1 哨兵。
- 代码提示：辅助函数 `height(root) int`，差异 >1 返回 -1 向上传。
- Go 要点：避免暴力重复算高度。

## 打卡清单
- [ ] 跑通生产者消费者模型
- [ ] 理解缓冲队列的解耦作用
- [ ] 知道什么时候 close channel
- [ ] 写完二叉树深度与平衡题
