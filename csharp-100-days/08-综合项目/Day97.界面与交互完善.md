# Day 97 · 界面与交互完善

## 今日目标
- 给 API 加一些易用性改进
- 统一返回格式
- 完善错误提示

## 学习内容

### 统一返回包装

```csharp
public class ApiResponse<T>
{
    public bool Ok { get; set; }
    public T? Data { get; set; }
    public string? Error { get; set; }
}
```

简单项目可以直接返回对象；大项目统一包装。

### 校验

```csharp
public class CreateTransactionDto
{
    [Range(0.01, 1_000_000)] public decimal Amount { get; set; }
    [Required] public string Note { get; set; } = "";
    public DateTime Date { get; set; }
    public int CategoryId { get; set; }
}
```

`[ApiController]` 自动 400。

### 分页

```csharp
[HttpGet]
public async Task<IActionResult> List([FromQuery] DateTime from, [FromQuery] DateTime to,
    [FromQuery] int page = 1, [FromQuery] int pageSize = 20, CancellationToken ct = default)
{
    var all = await _repo.QueryAsync(from, to, ct);
    var paged = all.Skip((page - 1) * pageSize).Take(pageSize).ToList();
    return Ok(new { total = all.Count(), page, pageSize, items = paged });
}
```

### Swagger 注释

给 Controller 加 XML 注释，Swagger 会显示。

## 核心重点
- 校验用数据注解
- 列表加分页
- 错误提示友好
- 统一返回格式
- 好 API 是用出来的

## 练习/任务
1. 给 Create 加 DTO 校验
2. 列表接口加分页
3. 错误返回改成中文友好提示
4. （选做）给 Swagger 加 XML 注释

## 今日刷题
- **LeetCode 146 LRU 缓存（复习）**：Dictionary + LinkedList。
- **LeetCode 460 LFU 缓存（复习）**。

## 打卡清单
- [ ] DTO 校验加好
- [ ] 列表分页
- [ ] 错误提示友好
- [ ] 已完成至少一道今日刷题
