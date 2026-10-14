# Day 88 · 并发与工程化综合练习

## 今日目标
- 综合运用多线程、测试、日志、配置、部署
- 给 Todo API 加上工程化能力
- 复盘整个阶段

## 学习内容

### 本阶段回顾

| 天 | 主题 |
|---|---|
| 76-77 | 线程、线程池、Task.Run |
| 78-79 | 锁、并发集合 |
| 80-81 | 并行、取消 |
| 82-83 | xUnit 单元测试 |
| 84-85 | 日志、配置 |
| 86-87 | NuGet、发布部署 |

### 给 Todo API 加工程化

1. **日志**：Controller 注入 ILogger，每个接口打 Info 日志；
2. **配置**：appsettings.json 加 `TodoOptions { MaxTodos = 100 }`；
3. **测试**：给 Calculator/服务层写 xUnit；
4. **健康检查**：加 `/health`；
5. **发布**：dotnet publish。

### 关键代码片段

```csharp
// Program.cs 加日志与健康检查
builder.Services.AddHealthChecks();
builder.Services.AddSingleton<IStartupFilter, ...>();   // 略

// Controller
public TodosController(AppDbContext db, ILogger<TodosController> logger)
{
    _db = db;
    _logger = logger;
}

[HttpPost]
public async Task<IActionResult> Create(Todo todo)
{
    _logger.LogInformation("新建 Todo：{Title}", todo.Title);
    // ...
}
```

### 自查清单

- [ ] 业务代码有日志吗？
- [ ] 关键配置有校验吗？
- [ ] 核心服务有单元测试吗？
- [ ] 能 dotnet publish 一键发布吗？
- [ ] 有 /health 吗？

## 核心重点
- 并发：lock 小、优先并发集合、长任务可取消
- 测试：xUnit + AAA
- 日志：结构化、分级
- 配置：强类型 + 启动校验
- 工程化不是锦上添花，是上线必备

## 练习/任务
1. 给 Todo API 加 ILogger 日志
2. 给服务层写 3 个单元测试
3. 加 /health 并 publish
4. （选做）用 Serilog 替换默认日志输出到文件

## 今日刷题
- **LeetCode 169 多数元素（复习）**。
- **LeetCode 88 合并两个有序数组（复习）**。

## 打卡清单
- [ ] 给 API 加了日志
- [ ] 写了单元测试
- [ ] 成功 publish
- [ ] 已完成至少一道今日刷题
