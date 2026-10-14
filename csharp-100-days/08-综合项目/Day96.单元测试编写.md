# Day 96 · 单元测试编写

## 今日目标
- 给 StatisticsService 写测试
- 用假仓储替代真实数据库
- 跑通 dotnet test

## 学习内容

### 假仓储

```csharp
public class FakeTransactionRepository : ITransactionRepository
{
    public List<Transaction> Data { get; } = new();

    public Task AddAsync(Transaction t, CancellationToken ct) { Data.Add(t); return Task.CompletedTask; }
    public Task<int> SaveChangesAsync(CancellationToken ct) => Task.FromResult(1);
    public Task<Transaction?> GetByIdAsync(int id, CancellationToken ct) => Task.FromResult(Data.FirstOrDefault(x => x.Id == id));
    public Task<List<Transaction>> QueryAsync(DateTime from, DateTime to, CancellationToken ct)
        => Task.FromResult(Data.Where(t => t.Date >= from && t.Date <= to).ToList());
}
```

### 测试

```csharp
public class StatisticsServiceTests
{
    [Fact]
    public async Task TotalExpense_混合收支_只算支出()
    {
        var repo = new FakeTransactionRepository();
        repo.Data.AddRange(new[]
        {
            new Transaction { Amount = 100, Category = new Category{Type="Expense", Name="餐饮"} },
            new Transaction { Amount = 5000, Category = new Category{Type="Income", Name="工资"} },
            new Transaction { Amount = 200, Category = new Category{Type="Expense", Name="交通"} },
        });
        var svc = new StatisticsService(repo);

        var total = await svc.TotalExpenseAsync(DateTime.MinValue, DateTime.MaxValue, default);

        Assert.Equal(300, total);
    }
}
```

### 跑测试

```powershell
dotnet test
```

### 为什么用假仓储

- 测试快，不依赖数据库；
- 可构造任意数据；
- 测的是业务逻辑，不是 EF。

## 核心重点
- 测服务层，不测 Controller
- 假仓储实现接口
- AAA 三段
- dotnet test 一键跑
- 业务规则都该有测试

## 练习/任务
1. 实现 FakeTransactionRepository
2. 给 TotalExpenseAsync 写两个测试
3. 给 ByCategoryAsync 写一个测试
4. （选做）查 Moq 库，用 Moq 替代手写 Fake

## 今日刷题
- **LeetCode 146 LRU 缓存**：Dictionary + 双向链表。C# 用 LinkedList 实现。
- **LeetCode 460 LFU 缓存**：多级 Dictionary，频率桶。

## 打卡清单
- [ ] 假仓储写好
- [ ] 服务层测试通过
- [ ] dotnet test 全绿
- [ ] 已完成至少一道今日刷题
