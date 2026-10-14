# Day 79 · 线程安全集合

## 今日目标
- 认识 `System.Collections.Concurrent` 命名空间
- 会用 `ConcurrentDictionary`、`BlockingCollection`
- 避免自己加锁

## 学习内容

### 为什么需要并发集合

普通 `Dictionary` 多线程同时写会炸。.NET 提供内置线程安全版本：

| 并发集合 | 对应普通集合 | 用途 |
|---|---|---|
| `ConcurrentDictionary<K,V>` | Dictionary | 多线程键值读写 |
| `ConcurrentBag<T>` | Bag/无序 | 多线程加元素 |
| `ConcurrentQueue<T>` | Queue | 生产者-消费者 |
| `ConcurrentStack<T>` | Stack | 多线程栈 |
| `BlockingCollection<T>` | — | 阻塞式队列 |

### ConcurrentDictionary

```csharp
var dict = new ConcurrentDictionary<string, int>();

Parallel.For(0, 1000, i =>
{
    dict.AddOrUpdate($"key{i % 10}", 1, (k, v) => v + 1);
});

Console.WriteLine(dict["key5"]);   // 100
```

不用自己 lock。

### BlockingCollection：生产者-消费者

```csharp
var buffer = new BlockingCollection<int>(boundedCapacity: 5);

// 生产者
var producer = Task.Run(() =>
{
    for (int i = 0; i < 10; i++)
    {
        buffer.Add(i);
        Console.WriteLine($"生产 {i}");
    }
    buffer.CompleteAdding();
});

// 消费者
var consumer = Task.Run(() =>
{
    foreach (var item in buffer.GetConsumingEnumerable())
    {
        Console.WriteLine($"消费 {item}");
    }
});

await Task.WhenAll(producer, consumer);
```

队列满了生产者阻塞，空了消费者阻塞。

### 什么时候用并发集合

- 多线程同时读写同一个集合；
- 别在单线程里用，有额外开销；
- 别把 ConcurrentDictionary 当 Dictionary 用，API 不太一样。

## 核心重点
- Concurrent* 集合内置线程安全
- ConcurrentDictionary 多线程字典
- BlockingCollection 做生产者-消费者
- 比自己 lock 更省心
- 单线程场景别用，有开销

## 练习/任务
1. 用 ConcurrentDictionary 做一个多线程计数器
2. 用 BlockingCollection 写一个简单任务队列：两个生产者三个消费者
3. 对比 ConcurrentDictionary 与加锁的 Dictionary 性能
4. （选做）查 `Channel<T>`（.NET Core 3+）是什么

## 今日刷题
- **LeetCode 102 层序遍历（复习）**：Queue。
- **LeetCode 103 锯齿层序（复习）**：BFS。

## 打卡清单
- [ ] 会用 ConcurrentDictionary
- [ ] 会写生产者-消费者
- [ ] 知道并发集合的取舍
- [ ] 已完成至少一道今日刷题
