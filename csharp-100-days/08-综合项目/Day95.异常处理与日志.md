# Day 95 · 异常处理与日志

## 今日目标
- 加全局异常中间件
- 给关键接口加日志
- 区分预期错误与未预期错误

## 学习内容

### 全局异常中间件

```csharp
app.UseExceptionHandler(err => err.Run(async ctx =>
{
    var logger = ctx.RequestServices.GetRequiredService<ILogger<Program>>();
    var ex = ctx.Features.Get<IExceptionHandlerFeature>()?.Error;
    logger.LogError(ex, "未处理异常");
    ctx.Response.StatusCode = 500;
    await ctx.Response.WriteAsJsonAsync(new { error = "服务器开小差了" });
}));
```

放最外层，所有未捕获异常都到这。

### Controller 里的日志

```csharp
[HttpPost]
public async Task<IActionResult> Create([FromBody] Transaction input, CancellationToken ct)
{
    _logger.LogInformation("新增一笔 {Amount} 元，类别 {Cat}", input.Amount, input.CategoryId);
    await _repo.AddAsync(input, ct);
    await _repo.SaveChangesAsync(ct);
    return CreatedAtAction(nameof(List), new { }, input);
}
```

### 业务异常 → 400/404

```csharp
if (input.Amount <= 0)
{
    _logger.LogWarning("金额非法：{Amount}", input.Amount);
    return BadRequest(new { error = "金额必须大于 0" });
}
```

别把业务错误包成 500。

### 日志该打什么

- 关键业务动作（加账、删账）打 Info；
- 参数非法打 Warn；
- 未预期异常打 Error；
- 别打密码、身份证等敏感信息。

## 核心重点
- 全局异常中间件兜底
- 业务错误返回 4xx
- 未预期异常才 500
- 关键动作打 Info
- 别打敏感信息

## 练习/任务
1. 加全局异常中间件
2. 给 Create 接口加日志
3. 金额 ≤ 0 时返回 400
4. （选做）故意抛异常，看中间件返回什么

## 今日刷题
- **LeetCode 23 合并 K 个升序链表（复习）**：PriorityQueue。
- **LeetCode 148 排序链表（复习）**：归并。

## 打卡清单
- [ ] 全局异常中间件加好
- [ ] Controller 有日志
- [ ] 业务错误返回 4xx
- [ ] 已完成至少一道今日刷题
