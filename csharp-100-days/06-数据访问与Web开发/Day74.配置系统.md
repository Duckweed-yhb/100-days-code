# Day 74 · 配置系统

## 今日目标
- 理解 ASP.NET Core 配置来源优先级
- 会用 appsettings.json 与环境变量
- 会用 IOptions 读强类型配置

## 学习内容

### 配置从哪来

ASP.NET Core 配置按优先级从高到低：

1. 命令行参数
2. 环境变量
3. `appsettings.{Environment}.json`
4. `appsettings.json`
5. 默认值

### appsettings.json

```json
{
  "Logging": {
    "LogLevel": {
      "Default": "Information"
    }
  },
  "ConnectionStrings": {
    "Default": "Data Source=app.db"
  },
  "WeatherApi": {
    "ApiKey": "abc123",
    "TimeoutSeconds": 5
  }
}
```

### 直接读 IConfiguration

```csharp
var builder = WebApplication.CreateBuilder(args);
var config = builder.Configuration;

string conn = config.GetConnectionString("Default")!;
string apiKey = config["WeatherApi:ApiKey"]!;
int timeout = config.GetValue<int>("WeatherApi:TimeoutSeconds");
```

### 强类型 IOptions（推荐）

```csharp
public class WeatherApiOptions
{
    public const string Section = "WeatherApi";
    public string ApiKey { get; set; } = "";
    public int TimeoutSeconds { get; set; }
}

// Program.cs
builder.Services.Configure<WeatherApiOptions>(
    builder.Configuration.GetSection(WeatherApiOptions.Section));

// Controller 注入
public class WeatherController : ControllerBase
{
    private readonly WeatherApiOptions _opt;
    public WeatherController(IOptions<WeatherApiOptions> opt) => _opt = opt.Value;
}
```

### 环境区分

`Development` / `Staging` / `Production`：开发时用 `appsettings.Development.json`，生产用 `appsettings.Production.json`，密钥别进 git。

## 核心重点
- 配置来源多层，命令行/环境变量压过 json
- appsettings.json 放默认配置
- IConfiguration 直接读，IOptions 强类型读
- 连接串放 ConnectionStrings
- 密钥走环境变量，别进 git

## 练习/任务
1. 在 appsettings.json 加一个 `SiteName`，在首页输出
2. 加 ConnectionStrings:Default，传给 DbContext
3. 用 IOptions\<T> 读一段配置
4. （选做）设一个环境变量覆盖 SiteName，观察优先级

## 今日刷题
- **LeetCode 219 存在重复元素 II（复习）**：Dictionary 记上次位置。
- **LeetCode 228 汇总区间（复习）**：遍历分段。

## 打卡清单
- [ ] 会读 appsettings.json
- [ ] 会用 IOptions 强类型读配置
- [ ] 理解配置优先级
- [ ] 已完成至少一道今日刷题
