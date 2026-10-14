# Day 92 · 实现领域服务

## 今日目标
- 在 Core 写业务服务
- 把统计逻辑放服务层
- 保持 Controller 瘦

## 学习内容

### 服务层

业务逻辑放服务，Controller 只做参数绑定与 HTTP：

```csharp
// Budget.Core/Services/StatisticsService.cs
public class StatisticsService
{
    private readonly ITransactionRepository _repo;
    public StatisticsService(ITransactionRepository repo) { _repo = repo; }

    public async Task<decimal> TotalExpenseAsync(DateTime from, DateTime to, CancellationToken ct)
    {
        var list = await _repo.QueryAsync(from, to, ct);
        return list.Where(t => t.Category!.Type == "Expense").Sum(t => t.Amount);
    }

    public async Task<Dictionary<string, decimal>> ByCategoryAsync(DateTime from, DateTime to, CancellationToken ct)
    {
        var list = await _repo.QueryAsync(from, to, ct);
        return list
            .Where(t => t.Category!.Type == "Expense")
            .GroupBy(t => t.Category!.Name)
            .ToDictionary(g => g.Key, g => g.Sum(t => t.Amount));
    }
}
```

### 仓储实现（Infrastructure）

```csharp
public class TransactionRepository : ITransactionRepository
{
    private readonly BudgetDbContext _db;
    public TransactionRepository(BudgetDbContext db) { _db = db; }

    public async Task<List<Transaction>> QueryAsync(DateTime from, DateTime to, CancellationToken ct)
        => await _db.Transactions
                    .Include(t => t.Category)
                    .Where(t => t.Date >= from && t.Date <= to)
                    .ToListAsync(ct);

    public async Task AddAsync(Transaction t, CancellationToken ct)
    {
        await _db.Transactions.AddAsync(t, ct);
    }

    public async Task<Transaction?> GetByIdAsync(int id, CancellationToken ct)
        => await _db.Transactions.Include(t => t.Category).FirstOrDefaultAsync(t => t.Id == id, ct);

    public async Task<int> SaveChangesAsync(CancellationToken ct)
        => await _db.SaveChangesAsync(ct);
}
```

### 为什么分服务与仓储

- 仓储管"怎么存"；
- 服务管"业务规则"；
- Controller 管 HTTP。

## 核心重点
- 业务逻辑放服务层
- 仓储只管存取
- Controller 要瘦
- 依赖接口，可测试
- LINQ 写统计很顺手

## 练习/任务
1. 实现 TransactionRepository
2. 写 StatisticsService.TotalExpenseAsync
3. 写 ByCategoryAsync 分组统计
4. （选做）加"环比上月"方法

## 今日刷题
- **LeetCode 560 和为 K 的子数组（复习）**：前缀和 + Dictionary。
- **LeetCode 238 除自身以外数组的乘积**：前后缀积，O(1) 空间。

## 打卡清单
- [ ] 仓储实现完成
- [ ] 服务层统计方法写好
- [ ] 业务逻辑不在 Controller
- [ ] 已完成至少一道今日刷题
