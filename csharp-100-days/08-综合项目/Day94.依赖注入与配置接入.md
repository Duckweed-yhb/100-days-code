# Day 94 · 依赖注入与配置接入

## 今日目标
- 在 Program.cs 注册所有服务
- 接入配置
- 确认 DI 链路通

## 学习内容

### Program.cs

```csharp
var builder = WebApplication.CreateBuilder(args);

builder.Services.AddControllers();
builder.Services.AddEndpointsApiExplorer();
builder.Services.AddSwaggerGen();

// DbContext
builder.Services.AddDbContext<BudgetDbContext>(opt =>
    opt.UseSqlite(builder.Configuration.GetConnectionString("Default")));

// 仓储与服务
builder.Services.AddScoped<ITransactionRepository, TransactionRepository>();
builder.Services.AddScoped<StatisticsService>();

// 强类型配置
builder.Services.Configure<BudgetOptions>(
    builder.Configuration.GetSection(BudgetOptions.Section));

var app = builder.Build();

if (app.Environment.IsDevelopment())
{
    app.UseSwagger();
    app.UseSwaggerUI();
}

app.MapControllers();
app.Run();
```

### BudgetOptions

```csharp
public class BudgetOptions
{
    public const string Section = "Budget";
    public decimal MonthlyBudget { get; set; }
}
```

appsettings.json：

```json
{
  "ConnectionStrings": {
    "Default": "Data Source=budget.db"
  },
  "Budget": {
    "MonthlyBudget": 5000
  }
}
```

### 验证一下

跑起来，加一笔账，看数据库文件生成；调用统计接口看返回。

## 核心重点
- AddScoped 注册仓储与服务
- AddDbContext 注册 EF
- 配置走 appsettings.json
- Controller 构造函数自动收依赖
- DI 链路通，整个系统才能跑

## 练习/任务
1. 把所有服务注册进 DI
2. 加 BudgetOptions 配置
3. 跑一遍完整流程：加账、查账、查统计
4. （选做）在 StatisticsService 里注入 IOptions\<BudgetOptions>，超预算时打 Warn 日志

## 今日刷题
- **LeetCode 148 排序链表**：归并排序。思路：快慢指针找中点，递归 merge。
- **LeetCode 23 合并 K 个升序链表**：优先队列（小根堆）。C# 用 PriorityQueue。

## 打卡清单
- [ ] DI 注册完成
- [ ] 配置接入
- [ ] 完整流程跑通
- [ ] 已完成至少一道今日刷题
