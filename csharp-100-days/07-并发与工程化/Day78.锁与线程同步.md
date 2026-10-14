# Day 78 · 锁与线程同步

## 今日目标
- 理解多线程共享数据为什么会出问题
- 会用 `lock` 保护共享状态
- 认识 `SemaphoreSlim` 限并发

## 学习内容

### 竞态条件

```csharp
int count = 0;
Parallel.For(0, 1000, _ => count++);
Console.WriteLine(count);   // 不是 1000！
```

`count++` 看似一步，实际是"读-改-写"三步。多线程同时读，都读到 0，都写 1，就丢了更新。

### lock：互斥锁

```csharp
int count = 0;
object lockObj = new();

Parallel.For(0, 1000, _ =>
{
    lock (lockObj)
    {
        count++;
    }
});
Console.WriteLine(count);   // 1000
```

同一时刻只有一个线程能进 `lock` 块，其他排队。

### 锁的对象

```csharp
private readonly object _lock = new();   // 专用锁对象
```

别 lock(this)、别 lock(typeof(X))，容易被外面误锁。用私有 readonly 对象。

### SemaphoreSlim：限并发

```csharp
var ss = new SemaphoreSlim(3);   // 最多 3 个同时进

await Task.WhenAll(Enumerable.Range(1, 10).Select(async i =>
{
    await ss.WaitAsync();
    try
    {
        Console.WriteLine($"任务 {i} 开始");
        await Task.Delay(1000);
    }
    finally { ss.Release(); }
}));
```

10 个任务同时来，但只跑 3 个，其他排队。

### 锁的代价

- 锁住就串行化，降低并行度；
- 死锁：A 等 B 释放，B 等 A 释放；
- 尽量锁小范围。

### 优先用无锁方案

- 共享数据用并发集合（Day79）；
- 用 `Interlocked.Increment(ref count)` 做简单计数；
- 不可变数据天然线程安全。

## 核心重点
- 多线程改共享变量会竞态
- lock 保证同一时刻一个线程进
- 用私有 readonly 对象做锁
- SemaphoreSlim 限并发数
- 锁要小，能不用就不用

## 练习/任务
1. 复现 count++ 竞态，再用 lock 修好
2. 用 SemaphoreSlim 限制同时下载最多 3 个
3. 查 `Interlocked.Increment` 怎么用，替换 count++
4. （选做）故意写两个互相 lock 的死锁例子，观察现象

## 今日刷题
- **LeetCode 141 环形链表（复习）**：快慢指针。
- **LeetCode 142 环形链表 II（复习）**：找入口。

## 打卡清单
- [ ] 理解竞态条件
- [ ] 会用 lock 保护共享数据
- [ ] 会用 SemaphoreSlim 限并发
- [ ] 已完成至少一道今日刷题
