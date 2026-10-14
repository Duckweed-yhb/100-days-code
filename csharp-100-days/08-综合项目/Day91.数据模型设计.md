# Day 91 · 数据模型设计

## 今日目标
- 在 Core 里定义实体
- 设计好字段、关系、约束
- 写第一个迁移

## 学习内容

### 实体定义

```csharp
// Budget.Core/Entities/Category.cs
public class Category
{
    public int Id { get; set; }
    public string Name { get; set; } = "";       // 餐饮
    public string Type { get; set; } = "Expense"; // Expense / Income
}

// Budget.Core/Entities/Transaction.cs
public class Transaction
{
    public int Id { get; set; }
    public decimal Amount { get; set; }           // 金额，用 decimal
    public string Note { get; set; } = "";
    public DateTime Date { get; set; }

    public int CategoryId { get; set; }
    public Category? Category { get; set; }        // 导航
}
```

### DbContext（Infrastructure）

```csharp
public class BudgetDbContext : DbContext
{
    public BudgetDbContext(DbContextOptions<BudgetDbContext> opt) : base(opt) { }
    public DbSet<Category> Categories => Set<Category>();
    public DbSet<Transaction> Transactions => Set<Transaction>();

    protected override void OnModelCreating(ModelBuilder mb)
    {
        mb.Entity<Transaction>()
          .Property(t => t.Amount)
          .HasPrecision(18, 2);
    }
}
```

### 接口（Core）

```csharp
public interface ITransactionRepository
{
    Task<Transaction?> GetByIdAsync(int id, CancellationToken ct);
    Task<List<Transaction>> QueryAsync(DateTime from, DateTime to, CancellationToken ct);
    Task AddAsync(Transaction t, CancellationToken ct);
    Task<int> SaveChangesAsync(CancellationToken ct);
}
```

### 迁移

```powershell
cd src/Budget.Api
dotnet ef migrations add Init --project ../Budget.Infrastructure --startup-project .
dotnet ef database update
```

## 核心重点
- 实体放 Core，DbContext 放 Infrastructure
- 金额用 decimal
- 外键 + 导航属性
- 仓储接口在 Core，实现在 Infrastructure
- 迁移版本化表结构

## 练习/任务
1. 定义 Category 与 Transaction 实体
2. 写 DbContext 与配置
3. 加迁移并 update 数据库
4. （选做）查 Fluent API HasPrecision 怎么用

## 今日刷题
- **LeetCode 53 最大子数组和（复习）**：Kadane。
- **LeetCode 121 买卖股票最佳时机（复习）**：本质上就是最大子段和。

## 打卡清单
- [ ] 实体定义完成
- [ ] DbContext 配好
- [ ] 迁移跑通
- [ ] 已完成至少一道今日刷题
