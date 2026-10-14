# Day 85 · 配置与选项模式

## 今日目标
- 深入 IOptions\<T> 与 IOptionsMonitor
- 会验证配置
- 知道配置热更新

## 学习内容

### 回顾 IOptions

```csharp
builder.Services.Configure<WeatherApiOptions>(
    builder.Configuration.GetSection("WeatherApi"));

public class WeatherController
{
    public WeatherController(IOptions<WeatherApiOptions> opt) { ... }
}
```

### IOptionsSnapshot：请求级、可热更新

```csharp
public class WeatherController
{
    public WeatherController(IOptionsSnapshot<WeatherApiOptions> opt)
    {
        var settings = opt.Value;
    }
}
```

- `IOptions<T>`：单例，整个应用一份；
- `IOptionsSnapshot<T>`：每个请求重新读，可热更新；
- `IOptionsMonitor<T>`：后台监听变化，回调通知。

### 配置校验

```csharp
builder.Services.AddOptions<WeatherApiOptions>()
    .Bind(builder.Configuration.GetSection("WeatherApi"))
    .ValidateDataAnnotations()
    .Validate(o => !string.IsNullOrEmpty(o.ApiKey), "ApiKey 不能为空")
    .ValidateOnStart();    // 启动时就校验
```

```csharp
public class WeatherApiOptions
{
    [Required] public string ApiKey { get; set; } = "";
    [Range(1, 60)] public int TimeoutSeconds { get; set; }
}
```

启动时配置不对直接报错，别等运行到才崩。

### 命名选项（了解）

```csharp
builder.Services.Configure<WeatherApiOptions("cn", o => o.TimeoutSeconds = 3);
builder.Services.Configure<WeatherApiOptions("us", o => o.TimeoutSeconds = 10);

// 注入 IOptionsMonitor<WeatherApiOptions>，按名取
```

## 核心重点
- IOptions 单例，IOptionsSnapshot 请求级热更新
- ValidateDataAnnotations + ValidateOnStart 启动校验
- 配置错了启动就崩，别拖到运行时
- 复杂配置用强类型 Options
- 别到处读 IConfiguration

## 练习/任务
1. 给 Todo API 加一个 `SiteOptions`（Name、MaxTodos）
2. 加配置校验：MaxTodos > 0
3. 改 appsettings.json 用 IOptionsSnapshot 观察热更新
4. （选做）用 IOptionsMonitor 监听配置变化

## 今日刷题
- **LeetCode 104 最大深度（复习）**。
- **LeetCode 110 平衡二叉树（复习）**。

## 打卡清单
- [ ] 会用强类型 Options
- [ ] 会加配置校验
- [ ] 理解三种 Options 的区别
- [ ] 已完成至少一道今日刷题
