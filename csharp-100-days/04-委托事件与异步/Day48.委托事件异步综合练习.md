# Day 48 · 委托事件异步综合练习

## 今日目标
- 综合运用委托、Lambda、事件、异步
- 写一个"任务调度器"小工具
- 复盘本阶段

## 学习内容

### 本阶段回顾

| 天 | 主题 |
|---|---|
| 38-39 | 委托、Action/Func |
| 40 | Lambda 与闭包 |
| 41 | 事件发布订阅 |
| 42-43 | 异常处理与自定义异常 |
| 44-47 | Task、async/await、并行、陷阱 |

### 示范：异步任务调度器

需求：注册若干异步任务，按顺序或并行执行，每个任务完成时发事件通知。

```csharp
class TaskRunner
{
    public event EventHandler<string>? TaskCompleted;

    public async Task RunAllAsync(IEnumerable<Func<Task>> tasks, bool parallel = false)
    {
        if (parallel)
        {
            var all = tasks.Select(async t =>
            {
                await t();
                TaskCompleted?.Invoke(this, "一个并行任务完成");
            });
            await Task.WhenAll(all);
        }
        else
        {
            foreach (var t in tasks)
            {
                await t();
                TaskCompleted?.Invoke(this, "一个串行任务完成");
            }
        }
    }
}
```

```csharp
TaskRunner runner = new();
runner.TaskCompleted += (s, msg) => Console.WriteLine($"[事件] {msg}");

var tasks = new Func<Task>[]
{
    async () => { await Task.Delay(500); Console.WriteLine("任务 A"); },
    async () => { await Task.Delay(300); Console.WriteLine("任务 B"); },
    async () => { await Task.Delay(700); Console.WriteLine("任务 C"); },
};

await runner.RunAllAsync(tasks, parallel: true);
```

### 综合自查

- 委托：会用 Action/Func 传方法吗？
- 事件：会用 event 做发布订阅吗？
- Lambda：闭包理解了吗？
- 异常：try-catch-finally 会写吗？
- 异步：async/await 一路到底了吗？

## 核心重点
- 委托让方法可传递；事件让对象松耦合通信
- Lambda 是委托的简写
- 异常是错误处理的标准机制
- async/await 让异步代码读起来像同步
- 这阶段结束，你已能写现代 C# 服务端代码

## 练习/任务
1. 实现上面的 TaskRunner，支持串行与并行两种模式
2. 给 TaskRunner 加 CancellationToken 参数，可中途取消
3. 加事件：每个任务开始时也发事件，订阅者打印"开始执行 X"
4. （选做）给任务加超时：单个任务超过 2 秒就取消并记录

## 今日刷题
- **LeetCode 94 二叉树中序遍历**：递归简单，迭代用栈。今天写迭代版。
- **LeetCode 144 二叉树前序遍历**：迭代用栈，注意压栈顺序（先压右再压左）。

## 打卡清单
- [ ] 已完成 TaskRunner 小工具
- [ ] 会用事件通知任务进度
- [ ] 会用 CancellationToken 取消
- [ ] 已完成至少一道今日刷题
