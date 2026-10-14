# Day 26 · goroutine协程

## 今日目标
- 理解 goroutine 是什么，与线程的区别
- 学会启动 goroutine
- 理解主函数退出会带走所有 goroutine

## 学习内容

### 并发 vs 并行

- **并发**：同时处理很多事，快速切换注意力
- **并行**：真的在多个 CPU 上同时执行

Go 的 goroutine 是轻量级用户态协程，由 Go 运行时调度，不是操作系统线程。

### 启动 goroutine

在任何函数调用前加 `go` 就启动一个 goroutine：

```go
func say(s string) {
    for i := 0; i < 3; i++ {
        fmt.Println(s, i)
    }
}

func main() {
    go say("world")    // 异步跑
    say("hello")       // 主 goroutine 同步跑
}
```

### 关键坑：main 不等待

`main` 函数一返回，整个程序就退出，所有 goroutine 被杀死。上面例子里 world 可能还没跑几个就被结束了。

让它跑完需要同步机制——后面几天会学 channel 和 WaitGroup，今天先用 `time.Sleep` 临时凑：

```go
func main() {
    go say("world")
    time.Sleep(1 * time.Second)
    say("hello")
}
```

### goroutine 有多轻

一个 goroutine 初始栈只有约 2KB，可动态增长；一台机器开几万个 goroutine 很正常，而 OS 线程通常几兆起步、开几千就吃力。

## 核心重点
- `go f(args)` 启动一个 goroutine，非阻塞
- goroutine 比线程轻得多，可轻松开成千上万个
- main 返回会杀掉所有 goroutine，必须显式等待
- 现在用 Sleep 临时等，后面用 channel/WaitGroup 正式做
- 不要忘记同步，否则输出错乱或丢失

## 练习/任务
1. 启动 3 个 goroutine 分别打印不同名字，观察输出顺序
2. 故意不加 Sleep，观察 main 退出导致 goroutine 没跑完
3. 写一个循环启动 10 个 goroutine，每个打印自己的编号
4. （选做）打印当前 goroutine 数量的体感：开 1 万个 goroutine 看是否流畅

## 今日刷题

### 1. LeetCode 9 回文数
- 思路：反转一半数字，与前半比较；或转字符串用双指针。
- 代码提示：负数直接 false；不断 `x % 10` 取末位拼到 reversed，`x /= 10`。
- Go 要点：整数运算，注意 `x == reversed/10`（奇数位中间位忽略）。

### 2. LeetCode 14 最长公共前缀
- 思路：以第一个字符串为基准，逐字符与其他串比较，不一致就截断。
- 代码提示：双层循环，外层遍历第一个串下标，内层遍历每个字符串。
- Go 要点：字符串用 `[]rune` 处理更稳；边界（空串、空切片）先判。

## 打卡清单
- [ ] 会用 go 启动 goroutine
- [ ] 理解 main 退出会杀死子 goroutine
- [ ] 体会 goroutine 的轻量
- [ ] 写完两道回文/前缀题
