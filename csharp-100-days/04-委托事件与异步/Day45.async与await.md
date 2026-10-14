# Day 45 · async与await

## 今日目标
- 掌握 `async` / `await` 写法
- 理解"异步方法像同步一样读"
- 在控制台里跑通第一个真正的异步程序

## 学习内容

### async / await 长什么样

```csharp
Console.WriteLine("开始");
string data = await DownloadAsync();
Console.WriteLine($"拿到 {data}");
Console.WriteLine("结束");

static async Task<string> DownloadAsync()
{
    await Task.Delay(2000);       // 模拟网络 2 秒
    return "页面内容";
}
```

读起来像同步代码，但 `await` 处不会阻塞线程——该线程被释放去干别的，等任务完成再回来继续。

### 规则

1. 方法加 `async` 关键字；
2. 返回值：无返回用 `Task`，有返回用 `Task<T>`；
3. 方法名以 `Async` 结尾是约定；
4. `await` 只能在 `async` 方法里用。

### 错误写法

```csharp
// 错：返回 void（除非是事件处理器）
async void BadMethod() { await Task.Delay(100); }

// 错：async 方法里没有 await
async Task<int> NoAwait() { return 42; }   // 警告
```

### 异步方法的执行

```csharp
Console.WriteLine("1");
await DoAsync();
Console.WriteLine("4");

static async Task DoAsync()
{
    Console.WriteLine("2");
    await Task.Delay(1000);
    Console.WriteLine("3");
}
```

输出顺序：1, 2, 3, 4——像同步一样，但 await 处没卡线程。

### Main 也能 async

现代 C# 支持顶级语句直接 await：

```csharp
// Program.cs 顶部
await Task.Delay(1000);
Console.WriteLine("跑完");
```

## 核心重点
- async 方法返回 Task 或 Task\<T>
- await 等待任务但不阻塞线程
- 异步方法读起来像同步，但底层不卡线程
- 除事件处理器外别写 async void
- 方法名加 Async 后缀是约定

## 练习/任务
1. 写 `ReadFileAsync` 模拟读文件（Task.Delay 1 秒），用 await 调用
2. 写一个方法依次 await 三个 Task.Delay，看总耗时
3. 把上面改成"先同时启动三个任务再 await"，对比总耗时
4. （选做）在 async 方法里故意抛异常，用 try-catch 捕获

## 今日刷题
- **LeetCode 101 对称二叉树**：判断二叉树是否镜像对称。思路：递归比较左右子树。
- **LeetCode 104 最大深度（迭代版）**：用栈做 DFS，或队列做 BFS 求层数。

## 打卡清单
- [ ] 会写 async 方法并用 await 调用
- [ ] 理解 await 不阻塞线程
- [ ] 知道别写 async void
- [ ] 已完成至少一道今日刷题
