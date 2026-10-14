# Day 76 · 线程与Thread

## 今日目标
- 理解进程、线程、协程的区别
- 会用 `Thread` 类手动启线程
- 知道为什么现代代码优先用 Task 而不是 Thread

## 学习内容

### 进程与线程

- **进程**：操作系统分配资源的单位，一个 exe 是一个进程；
- **线程**：进程内执行代码的最小单位，共享进程内存；
- 一个进程可以有多线程，并行干多件事。

### 用 Thread 启线程

```csharp
using System.Threading;

var t = new Thread(() =>
{
    for (int i = 0; i < 5; i++)
        Console.WriteLine($"后台线程：{i}");
});

t.Start();

for (int i = 0; i < 5; i++)
    Console.WriteLine($"主线程：{i}");

t.Join();    // 等后台线程跑完
Console.WriteLine("结束");
```

### 常用成员

| API | 作用 |
|---|---|
| `Start()` | 启动线程 |
| `Join()` | 等它结束 |
| `Sleep(ms)` | 当前线程睡一会（不推荐生产） |
| `IsBackground` | 后台线程（主线程结束就跟着结束） |

### 为什么不推荐裸用 Thread

- 手动 new Thread 成本高，数量多时压力大；
- 调度麻烦；
- 现代 API（Task、async/await）已封装好。

> 知道 Thread 是什么即可，业务代码用 Task.Run（明天）。

### 主线程与后台线程

控制台程序主线程结束，整个进程就退出。前台线程会阻止退出；后台线程不会。

## 核心重点
- 进程 vs 线程：资源单位 vs 执行单位
- Thread 类手动控制线程
- Start 启动、Join 等待
- 现代代码用 Task 抽象，别裸 new Thread
- 多线程共享内存，要同步（Day78）

## 练习/任务
1. 启两个线程同时打印 1~10，观察输出交错
2. 用 Join 等线程结束再打印"完成"
3. 对比 Thread.Sleep(1000) 与 Task.Delay(1000) 的区别
4. （选做）查线程池 ThreadPool 是什么

## 今日刷题
- **LeetCode 104 二叉树最大深度（复习）**：递归。
- **LeetCode 110 平衡二叉树（复习）**：后序。

## 打卡清单
- [ ] 理解进程与线程
- [ ] 会用 Thread 启线程
- [ ] 知道为什么优先用 Task
- [ ] 已完成至少一道今日刷题
