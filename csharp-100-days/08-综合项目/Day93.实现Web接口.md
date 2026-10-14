# Day 93 · 实现Web接口

## 今日目标
- 写 TransactionsController
- 写 StatisticsController
- 用 Swagger 跑通

## 学习内容

### TransactionsController

```csharp
[ApiController]
[Route("api/[controller]")]
public class TransactionsController : ControllerBase
{
    private readonly ITransactionRepository _repo;
    public TransactionsController(ITransactionRepository repo) { _repo = repo; }

    [HttpGet]
    public async Task<IActionResult> List([FromQuery] DateTime from, [FromQuery] DateTime to, CancellationToken ct)
    {
        var list = await _repo.QueryAsync(from, to, ct);
        return Ok(list);
    }

    [HttpPost]
    public async Task<IActionResult> Create([FromBody] Transaction input, CancellationToken ct)
    {
        await _repo.AddAsync(input, ct);
        await _repo.SaveChangesAsync(ct);
        return CreatedAtAction(nameof(List), new { }, input);
    }
}
```

### StatisticsController

```csharp
[ApiController]
[Route("api/stats")]
public class StatsController : ControllerBase
{
    private readonly StatisticsService _svc;
    public StatsController(StatisticsService svc) { _svc = svc; }

    [HttpGet("total")]
    public async Task<IActionResult> Total([FromQuery] DateTime from, [FromQuery] DateTime to, CancellationToken ct)
    {
        var total = await _svc.TotalExpenseAsync(from, to, ct);
        return Ok(new { total });
    }

    [HttpGet("by-category")]
    public async Task<IActionResult> ByCategory([FromQuery] DateTime from, [FromQuery] DateTime to, CancellationToken ct)
    {
        var dict = await _svc.ByCategoryAsync(from, to, ct);
        return Ok(dict);
    }
}
```

### 启动后访问

```
GET  /api/transactions?from=2026-09-01&to=2026-09-30
POST /api/transactions  body: {amount, categoryId, date, note}
GET  /api/stats/total?from=...&to=...
GET  /api/stats/by-category?from=...&to=...
```

## 核心重点
- Controller 只管 HTTP，业务在 Service
- 加 CancellationToken 支持取消
- Swagger 直接测
- 返回合适状态码
- 路由清晰

## 练习/任务
1. 实现上面两个 Controller
2. 加一个 DELETE /api/transactions/{id}
3. 用 Swagger 加几笔账并查统计
4. （选做）加分页参数 page/pageSize

## 今日刷题
- **LeetCode 238 除自身以外数组的乘积（复习）**：前后缀。
- **LeetCode 560 和为 K 的子数组（复习）**：前缀和。

## 打卡清单
- [ ] TransactionsController 写好
- [ ] StatsController 写好
- [ ] Swagger 跑通
- [ ] 已完成至少一道今日刷题
