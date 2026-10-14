# Day 80 · 并行编程PLINQ

## 今日目标
- 会用 `Parallel.For/ForEach` 做数据并行
- 认识 PLINQ
- 理解并行不一定快

## 学习内容

### Parallel.ForEach

```csharp
var urls = new[] { "a.com", "b.com", "c.com" };

Parallel.ForEach(urls, url =>
{
    Console.WriteLine($"下载 {url}，线程 {Environment.CurrentManagedThreadId}");
});
```

多个 url 同时处理，自动用线程池。

### 带异步的并行

```csharp
await Parallel.ForEachAsync(urls, async (url, ct) =>
{
    await DownloadAsync(url, ct);
});
```

### PLINQ：并行 LINQ

```csharp
var result = Enumerable.Range(1, 1000000)
    .AsParallel()
    .Where(x => IsPrime(x))
    .ToList();
```

`AsParallel()` 把 LINQ 查询拆到多核跑。**注意**：PLINQ 有调度开销，小数据不一定快。

### 并行不一定快

| 场景 | 适合并行 |
|---|---|
| CPU 密集 + 大量独立数据 | ✅ |
| I/O 密集（网络/磁盘） | 用 async/await + WhenAll |
| 数据量小 | 串行反而快（调度开销） |
| 必须顺序执行 | 别并行 |

### 并行聚合要小心

```csharp
// 错：多线程同时改 sum
long sum = 0;
Parallel.ForEach(data, x => sum += Heavy(x));

// 对：用线程本地变量
long total = 0;
Parallel.ForEach(data,
    () => 0L,
    (x, state, local) => local + Heavy(x),
    local => Interlocked.Add(ref total, local));
```

## 核心重点
- Parallel.ForEach 数据并行
- ForEachAsync 配合异步
- AsParallel 让 LINQ 多核跑
- 并行有调度开销，小数据别用
- 并行聚合要用线程本地变量

## 练习/任务
1. 用 Parallel.ForEach 并行处理 10 个模拟下载
2. 用 PLINQ 找出 1~100000 的所有质数，对比串行耗时
3. 对比串行与并行处理 1000 个任务的总耗时
4. （选做）故意制造并行聚合问题，再修好

## 今日刷题
- **LeetCode 101 对称二叉树（复习）**：递归。
- **LeetCode 100 相同二叉树（复习）**：递归。

## 打卡清单
- [ ] 会用 Parallel.ForEach
- [ ] 会用 AsParallel
- [ ] 知道并行不是万能药
- [ ] 已完成至少一道今日刷题
