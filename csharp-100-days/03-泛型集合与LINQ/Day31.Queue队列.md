# Day 31 · Queue队列

## 今日目标
- 掌握 `Queue<T>` 先进先出（FIFO）
- 理解队列的典型应用场景
- 能用队列解决"排队"类问题

## 学习内容

### Queue 是什么

队列像排队买票：先来先服务。一头进（Enqueue），一头出（Dequeue）。

```csharp
Queue<string> q = new();
q.Enqueue("一号");
q.Enqueue("二号");
q.Enqueue("三号");

Console.WriteLine(q.Peek());    // 看队首，不取出：一号
Console.WriteLine(q.Dequeue()); // 取出队首：一号
Console.WriteLine(q.Dequeue()); // 二号
Console.WriteLine(q.Count);    // 1
```

### 常用操作

| 方法 | 作用 |
|---|---|
| `Enqueue(x)` | 入队（尾部加） |
| `Dequeue()` | 出队（头部取），空队列抛异常 |
| `Peek()` | 看队首，不取 |
| `Count` | 元素数 |
| `Clear()` | 清空 |

### 例子：打印任务队列

```csharp
Queue<string> printQueue = new();
printQueue.Enqueue("文档A.pdf");
printQueue.Enqueue("图片B.png");
printQueue.Enqueue("表格C.xlsx");

while (printQueue.Count > 0)
{
    string job = printQueue.Dequeue();
    Console.WriteLine($"正在打印：{job}");
}
```

### 用队列做广度优先（入门）

广度优先搜索（BFS）天然用队列——一层一层往外扩：

```csharp
// 给一个二叉树节点，按层打印
Queue<Node> q = new();
q.Enqueue(root);
while (q.Count > 0)
{
    Node cur = q.Dequeue();
    Console.WriteLine(cur.Value);
    if (cur.Left != null) q.Enqueue(cur.Left);
    if (cur.Right != null) q.Enqueue(cur.Right);
}
```

## 核心重点
- Queue\<T> 先进先出 FIFO
- Enqueue 入队、Dequeue 出队、Peek 看队首
- 适合排队、任务调度、BFS
- Dequeue 空队列会抛异常，先判 Count
- 不要用 Queue 做随机访问

## 练习/任务
1. 模拟银行叫号：入队顾客，每隔一秒叫一个号（用 Thread.Sleep 体验即可）
2. 用 Queue 实现 Day25 提到的"用队列实现栈"
3. 写方法 `ReverseQueue<T>(Queue<T> q)` 把队列反转
4. （选做）用队列做约瑟夫环：n 个人报数，报到 k 出列，求最后剩下谁

## 今日刷题
- **LeetCode 225 用队列实现栈**：之前 Day25 见过。思路：push 时正常入队；pop 时把前 n-1 个挪到备用队列尾部，剩下的就是栈顶。
- **LeetCode 933 最近的请求次数**：多次调 ping(t)，返回过去 3000ms 内的请求数。思路：队列存时间戳，每次把队首 < t-3000 的出队，剩下 Count 即答案。

## 打卡清单
- [ ] 会用 Queue 入队出队
- [ ] 理解 FIFO 语义
- [ ] 能用队列做简单任务调度
- [ ] 已完成至少一道今日刷题
