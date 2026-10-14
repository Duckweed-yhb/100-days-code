# Day 84 · 日志记录

## 今日目标
- 用内置 ILogger 写日志
- 理解日志级别
- 配置输出到控制台与文件

## 学习内容

### 为什么用日志而不是 Console.WriteLine

- 日志有级别（Debug/Info/Warn/Error），生产可过滤；
- 可结构化（JSON）、可输出到文件/远端；
- 带时间戳、类别、TraceId。

### 在 ASP.NET Core 里用 ILogger

```csharp
[ApiController]
[Route("api/[controller]")]
public class TodosController : ControllerBase
{
    private readonly ILogger<TodosController> _logger;
    public TodosController(ILogger<TodosController> logger) { _logger = logger; }

    [HttpGet]
    public IActionResult Get()
    {
        _logger.LogInformation("有人请求 Todo 列表");
        _logger.LogDebug("详细参数：{Query}", Request.Query);
        return Ok(new List<string>());
    }
}
```

### 日志级别

| 级别 | 用途 |
|---|---|
| Trace | 最细，调试细节 |
| Debug | 开发期细节 |
| Information | 正常流程 |
| Warning | 异常但能继续 |
| Error | 出错了 |
| Critical | 严重故障 |

生产一般设 Information 以上，Debug 不输出。

### 配置 appsettings.json

```json
{
  "Logging": {
    "LogLevel": {
      "Default": "Information",
      "Microsoft.AspNetCore": "Warning"
    }
  }
}
```

### 结构化日志

```csharp
_logger.LogInformation("用户 {UserId} 下单 {OrderId}", userId, orderId);
```

日志会变成结构化字段，方便 ELK 之类系统检索。

## 核心重点
- ILogger\<T> 由 DI 注入
- LogInformation/LogWarning/LogError 分级
- 用占位符 {X} 不要字符串拼接
- 生产过滤低级别日志
- 结构化日志便于检索

## 练习/任务
1. 在 Controller 里加 Info 与 Error 日志
2. 故意抛异常，观察 Error 日志输出
3. 改配置把 Microsoft 命名空间调成 Warning
4. （选做）查 Serilog 怎么接文件/ELK

## 今日刷题
- **LeetCode 121 买卖股票（复习）**。
- **LeetCode 122 买卖股票 II（复习）**。

## 打卡清单
- [ ] 会注入 ILogger
- [ ] 会用不同级别
- [ ] 会写结构化日志
- [ ] 已完成至少一道今日刷题
