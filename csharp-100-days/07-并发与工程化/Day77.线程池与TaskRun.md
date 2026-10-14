# Day 77 · 线程池与TaskRun

## 今日目标
- 理解线程池为什么高效
- 会用 Task.Run 跑 CPU 密集工作
- 区分 CPU 密集与 I/O 密集

## 学习内容

### 线程池

每次 new Thread 成本高。.NET 维护一个"线程池"：预先备好若干线程，任务来了直接用，用完归还，不反复创建销毁。

`Task.Run` 默认把任务丢到线程池：

```csharp
Task<int> t = Task.Run(() =>
{
    long sum = 0;
    for (long i = 0; i < 100_000_000; i++) sum += i;
    return (int)(sum % 1000);
});

Console.WriteLine("主线程干别的...");
int result = await t;
Console.WriteLine($"结果 {result}");
```

### CPU 密集 vs I/O 密集

| 类型 | 例子 | 用什么 |
|---|---|---|
| CPU 密集 | 大数据计算、图片处理 | Task.Run |
| I/O 密集 | 网络请求、读文件 | async/await 专用方法（不占线程） |

```csharp
// CPU 密集：Task.Run
await Task.Run(() => HeavyCompute());

// I/O 密集：直接 await，别包 Task.Run
await httpClient.GetStringAsync(url);
```

### 别滥用 Task.Run

ASP.NET Core 里别为了"异步"而 `await Task.Run(() => 同步方法)`——反而多了线程切换。该异步就用真正的异步 API。

### 并行加速

```csharp
// 串行
foreach (var job in jobs) Process(job);

// 并行：多个 CPU 核心同时跑
await Parallel.ForEachAsync(jobs, async (job, ct) => await ProcessAsync(job, ct));
```

## 核心重点
- 线程池复用线程，Task.Run 丢任务上去
- CPU 密集用 Task.Run
- I/O 密集用真正的 async API，别包 Task.Run
- Parallel.ForEachAsync 并行处理集合
- 别在 ASP.NET 里乱包 Task.Run

## 练习/任务
1. 用 Task.Run 算 1~1 亿的和，主线程同时打印"计算中"
2. 用 Parallel.ForEachAsync 并行处理 10 个模拟任务
3. 对比串行与并行处理 10 个延时任务的总耗时
4. （选做）查 `Environment.ProcessorCount` 看你有几核

## 今日刷题
- **LeetCode 121 买卖股票（复习）**：一次遍历。
- **LeetCode 122 买卖股票 II（复习）**：贪心。

## 打卡清单
- [ ] 理解线程池
- [ ] 会用 Task.Run 跑 CPU 活
- [ ] 区分 CPU 密集与 I/O 密集
- [ ] 已完成至少一道今日刷题
