# Day 44 · 异步背景与Task

## 今日目标
- 理解为什么需要异步：别让主线程干等
- 认识 `Task` 与 `Task<T>`
- 学会用 Task.Run 启动一个后台任务

## 学习内容

### 同步等待的问题

```csharp
Console.WriteLine("开始下载");
string data = DownloadFromServer();   // 假设要 3 秒
Console.WriteLine($"拿到 {data}");
Console.WriteLine("做别的事");
```

同步调用时，整个程序卡在 `DownloadFromServer()` 三秒，什么都不能干。在桌面应用里这就是"界面卡死"；在 Web 服务里就是"一个线程被浪费"。

### Task 是什么

`Task` 表示"一个将来会完成的工作"。它不阻塞当前线程，先把任务派出去，主线程继续干别的：

```csharp
Task<string> task = DownloadFromServerAsync();   // 立刻返回，不等结果
Console.WriteLine("下载中，先干点别的...");
string data = task.Result;                      // 真要结果时再等
Console.WriteLine($"拿到 {data}");
```

### Task.Run：把 CPU 密集工作扔后台

```csharp
Task<int> t = Task.Run(() =>
{
    int sum = 0;
    for (int i = 0; i < 10_000_000; i++) sum += i;
    return sum;
});

Console.WriteLine("后台算着，我先干别的");
int result = t.Result;
Console.WriteLine($"结果 {result}");
```

### Task 常用 API

| API | 作用 |
|---|---|
| `Task.Run(Func<T>)` | 在线程池启动任务 |
| `Task.Delay(ms)` | 非阻塞等待 |
| `Task.WhenAll(...)` | 等多个任务全完成 |
| `Task.WhenAny(...)` | 等任一完成 |
| `.Result` | 同步等结果（不推荐，明天讲 await） |

### 异步 ≠ 多线程

- 异步：等 I/O（网络、磁盘）时不占线程，让出资源；
- 多线程：CPU 密集活真的并行算。
- `Task.Run` 是多线程；下载网页那种是 I/O 异步。两者都用 Task 表达。

## 核心重点
- 异步解决"别干等 I/O"的问题
- Task 表示将来完成的工作
- Task.Run 把 CPU 活扔线程池
- .Result 能拿结果，但会阻塞，明天用 await
- Task.WhenAll 等多任务

## 练习/任务
1. 用 Task.Run 算 1~1000 万的和，主线程同时打印"计算中..."
2. 用 Task.Delay(1000) 模拟延时 1 秒，观察它与 Thread.Sleep 的区别
3. 启动三个 Task，分别 sleep 1/2/3 秒，用 WhenAll 等全部完成
4. （选做）用 WhenAny 看哪个任务先完成

## 今日刷题
- **LeetCode 104 二叉树最大深度**：递归 max(左深,右深)+1。今天顺便了解二叉树节点定义。
- **LeetCode 111 二叉树最小深度**：注意叶子节点判断：某子树为空时不能直接取 min。

## 打卡清单
- [ ] 理解同步等待的问题
- [ ] 会用 Task.Run 启动后台任务
- [ ] 知道 Task.WhenAll 的作用
- [ ] 已完成至少一道今日刷题
