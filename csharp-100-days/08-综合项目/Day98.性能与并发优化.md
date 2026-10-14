# Day 98 · 性能与并发优化

## 今日目标
- 找出性能瓶颈
- 加必要的索引
- 避免阻塞调用

## 学习内容

### 数据库索引

```csharp
modelBuilder.Entity<Transaction>()
    .HasIndex(t => t.Date);
```

按日期查询频繁就加索引。

### 异步一路到底

Controller 里所有 EF 调用都用 Async 版本：

```csharp
await _repo.QueryAsync(...);   // ✅
// _repo.Query(...);            // ❌ 同步阻塞
```

### 别在循环里查数据库

```csharp
// 错：N+1
foreach (var t in transactions)
{
    var c = await _db.Categories.FindAsync(t.CategoryId);
}

// 对：Include 一次连表
await _db.Transactions.Include(t => t.Category).ToListAsync();
```

### 加健康检查与 metrics

```csharp
builder.Services.AddHealthChecks();
// ...
app.MapHealthChecks("/health");
```

### 并发安全

服务是 Scoped，每个请求一个实例，天然线程安全。共享状态用并发集合。

## 核心重点
- 频繁查询的字段加索引
- 异步一路到底
- 避免 N+1
- /health 必加
- 先 Profiling 再优化

## 练习/任务
1. 给 Date 加索引并迁移
2. 检查 Controller 里有没有同步调用
3. 检查有没有 N+1 查询
4. （选做）用 BenchmarkDotNet 测一下统计方法

## 今日刷题
- **LeetCode 239 滑动窗口最大值**：单调队列。思路：Deque 存下标，保持队首最大。
- **LeetCode 76 最小覆盖子串**：滑动窗口 + 计数。

## 打卡清单
- [ ] 加了必要索引
- [ ] 异步一路到底
- [ ] N+1 修了
- [ ] 已完成至少一道今日刷题
