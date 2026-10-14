# Day 81 · 取消与超时

## 今日目标
- 会用 `CancellationToken` 取消长任务
- 会用 `Task.WhenAny` + Delay 做超时
- 在 Web API 里接收取消请求

## 学习内容

### CancellationToken 回顾

```csharp
async Task WorkAsync(CancellationToken ct)
{
    for (int i = 0; i < 100; i++)
    {
        ct.ThrowIfCancellationRequested();
        await Task.Delay(100, ct);
    }
}

using var cts = new CancellationTokenSource();
cts.CancelAfter(500);
try { await WorkAsync(cts.Token); }
catch (OperationCanceledException) { Console.WriteLine("已取消"); }
```

### 超时模式

```csharp
async Task<T> WithTimeout<T>(Task<T> task, TimeSpan timeout)
{
    var delay = Task.Delay(timeout);
    var finished = await Task.WhenAny(task, delay);
    if (finished == delay) throw new TimeoutException();
    return await task;
}
```

### Web API 里的取消

ASP.NET Core 自动把客户端断开的 CancellationToken 注入：

```csharp
[HttpGet]
public async Task<IActionResult> Download(CancellationToken ct)
{
    using var client = new HttpClient();
    var s = await client.GetStringAsync("https://example.com", ct);
    return Content(s);
}
```

客户端关页面，后端自动取消。

### 层级取消

```csharp
var cts = new CancellationTokenSource();
var child = CancellationTokenSource.CreateLinkedTokenSource(cts.Token);
// 父 cts.Cancel() 会自动取消 child
```

## 核心重点
- CancellationToken 协作式取消
- ThrowIfCancellationRequested 或传给异步 API
- WhenAny + Delay 做超时
- Web API 方法加 CancellationToken 参数
- 长任务都该支持取消

## 练习/任务
1. 写一个可取消的长循环，300ms 后取消
2. 给 HTTP 请求加 2 秒超时
3. 在 Controller 里加 CancellationToken 参数
4. （选做）用 CreateLinkedTokenSource 组合两个 token

## 今日刷题
- **LeetCode 112 路径总和（复习）**：递归。
- **LeetCode 113 路径总和 II（复习）**：DFS。

## 打卡清单
- [ ] 会用 CancellationToken 取消任务
- [ ] 会用 WhenAny 做超时
- [ ] 会在 Web API 接收取消
- [ ] 已完成至少一道今日刷题
