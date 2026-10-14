# Day 46 · 多任务异步并行

## 今日目标
- 学会 `Task.WhenAll` 并行启动多个异步任务
- 区分"依次 await"与"先启动再 await"
- 控制并发数与处理异常

## 学习内容

### 依次 await vs 并行

```csharp
// 依次：每个等前一个完成，总耗时 = 三者之和
await Task.Delay(1000);
await Task.Delay(2000);
await Task.Delay(3000);
// 共 6 秒

// 先启动：三个几乎同时跑，总耗时 ≈ 最长那个
var t1 = Task.Delay(1000);
var t2 = Task.Delay(2000);
var t3 = Task.Delay(3000);
await Task.WhenAll(t1, t2, t3);
// 共 3 秒
```

关键：**先把任务启动（赋给变量），再统一 await**。

### 并行下载多个网页

```csharp
static async Task Main()
{
    string[] urls = { "https://a.com", "https://b.com", "https://c.com" };

    // 每个 URL 启动一个任务
    Task<string>[] tasks = urls.Select(DownloadAsync).ToArray();

    // 等全部完成
    string[] results = await Task.WhenAll(tasks);

    foreach (var r in results) Console.WriteLine(r);
}

static async Task<string> DownloadAsync(string url)
{
    await Task.Delay(1000);   // 模拟网络
    return $"内容来自 {url}";
}
```

### WhenAny：等最快那个

```csharp
Task<string> finished = await await Task.WhenAny(t1, t2, t3);
Console.WriteLine($"先回来的是 {finished.Result}");
```

### 异常处理

```csharp
try
{
    await Task.WhenAll(t1, t2, t3);
}
catch (Exception ex)
{
    Console.WriteLine($"至少一个失败：{ex.Message}");
    // 实际 AggregateException 里包含所有异常
}
```

### 别无限制并发

同时发 10000 个 HTTP 请求会把目标服务器打死。生产中常用 `SemaphoreSlim` 限并发（Day78 讲）。

## 核心重点
- 先启动任务（Task 变量），再 WhenAll，才能并行
- 依次 await 是串行；WhenAll 是并行
- WhenAny 等最快那个
- 并行异常会包成 AggregateException
- 控制并发数，别打爆下游

## 练习/任务
1. 用 WhenAll 并行下载 5 个模拟任务，总耗时应≈最长那个
2. 对比"依次 await"与"WhenAll"的总耗时
3. 用 WhenAny 实现"最快返回的源优先"
4. （选做）故意让其中一个任务抛异常，观察 catch 到什么

## 今日刷题
- **LeetCode 102 二叉树层序遍历**：用 Queue 做 BFS，每层单独成一列。思路：每层开始前记录队列长度。
- **LeetCode 103 锯齿形层序遍历**：偶数层从左到右，奇数层从右到左。思路：BFS + 反转列表。

## 打卡清单
- [ ] 会用 WhenAll 并行多任务
- [ ] 理解先启动再 await 的关键
- [ ] 会用 WhenAny
- [ ] 已完成至少一道今日刷题
