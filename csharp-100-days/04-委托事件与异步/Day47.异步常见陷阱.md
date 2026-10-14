# Day 47 · 异步常见陷阱

## 今日目标
- 避开 async/await 新手常踩的坑
- 理解 `.Result` / `.Wait()` 的死锁风险
- 学会用 `ConfigureAwait`（了解）与取消

## 学习内容

### 陷阱一：.Result 死锁

```csharp
// 别这样：在 UI / ASP.NET 里容易死锁
void Bad()
{
    string result = DownloadAsync().Result;   // 阻塞等
}
```

`.Result` 会阻塞当前线程等任务完成；而任务完成后又要回到当前线程上下文——两边互相等，死锁。**永远用 await，不要 .Result**。

### 陷阱二：async void

```csharp
// 除事件处理器外别写
async void Bad() { await Task.Delay(100); }
```

async void 不能被 await，异常也无法捕获，崩了直接炸进程。

### 陷阱三：忘记 await

```csharp
async void Handler()
{
    FireAndForget();        // 忘了 await
    Console.WriteLine("结束");   // 可能 FireAndForget 还没跑完
}

async Task FireAndForget() { await Task.Delay(1000); }
```

要么 await，要么明确知道自己在"发射后不管"（并加 try-catch）。

### 陷阱四：在循环里并行过头

```csharp
// 错：同时发 1000 个请求
var tasks = urls.Select(u => DownloadAsync(u)).ToArray();
await Task.WhenAll(tasks);
```

要限并发，用 `SemaphoreSlim`（Day78）。

### 取消：CancellationToken

```csharp
async Task WorkAsync(CancellationToken ct)
{
    for (int i = 0; i < 100; i++)
    {
        ct.ThrowIfCancellationRequested();
        await Task.Delay(100, ct);
    }
}

using CancellationTokenSource cts = new();
cts.CancelAfter(500);     // 500ms 后取消
try { await WorkAsync(cts.Token); }
catch (OperationCanceledException) { Console.WriteLine("已取消"); }
```

### 经验法则

1. 一路 async 到顶，别在中间混 .Result；
2. 库代码优先 `ConfigureAwait(false)`（库不关心上下文）；
3. 长任务都接 CancellationToken；
4. 别在循环里无脑并行。

## 核心重点
- 别用 .Result / .Wait()，会死锁
- 别写 async void（事件处理器除外）
- 别忘 await
- 长任务接 CancellationToken
- 异步要"一路 async 到顶"

## 练习/任务
1. 写一个会被取消的长循环，用 CancellationTokenSource 500ms 后取消
2. 故意写一段 `.Result` 代码，理解为什么不推荐
3. 给并行下载加 SemaphoreSlim 限制最多同时 3 个
4. （选做）查 `IHttpClientFactory` 与 `HttpClient` 的关系（Web 阶段会用）

## 今日刷题
- **LeetCode 112 路径总和**：二叉树是否存在根到叶路径和等于 target。思路：递归减值，到叶子时判断。
- **LeetCode 113 路径总和 II**：找出所有路径和等于 target 的路径。思路：DFS + 回溯，走到叶子时记录。

## 打卡清单
- [ ] 知道 .Result 死锁风险
- [ ] 会用 CancellationToken 取消任务
- [ ] 理解 async void 的危害
- [ ] 已完成至少一道今日刷题
