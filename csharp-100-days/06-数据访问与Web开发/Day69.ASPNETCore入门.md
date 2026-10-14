# Day 69 · ASPNETCore入门

## 今日目标
- 创建第一个 ASP.NET Core Web 项目
- 理解最小 API 与 Program.cs
- 跑起来看到浏览器输出

## 学习内容

### 建项目

```powershell
dotnet new web -o WebDemo
cd WebDemo
dotnet run
```

控制台会显示监听地址（如 `http://localhost:5000`）。

### Program.cs 长这样（最小 API）

```csharp
var builder = WebApplication.CreateBuilder(args);
var app = builder.Build();

app.MapGet("/", () => "Hello, Web!");

app.MapGet("/hello", (string name) => $"你好，{name}");

app.Run();
```

浏览器访问 `http://localhost:5000` 看到 `Hello, Web!`；访问 `/hello?name=余泓彬` 看到 `你好，余泓彬`。

### Web 请求生命周期

```
浏览器请求
  → ASP.NET Core 服务器
  → 中间件管道（认证、路由、异常处理...）
  → 路由匹配到 MapGet
  → 执行委托，返回结果
  → 序列化 JSON/文本
  → 响应浏览器
```

### 三种模板

| 模板 | 用途 |
|---|---|
| `dotnet new web` | 最小 API，极简 |
| `dotnet new webapi` | Web API 控制器模板 |
| `dotnet new mvc` | MVC 带视图 |

本阶段先学最小 API，再过渡到控制器。

### 常用命令

```powershell
dotnet run                 # 运行
dotnet watch run           # 改代码自动重启（开发神器）
```

## 核心重点
- ASP.NET Core 是跨平台 Web 框架
- Program.cs 里 Build → Map → Run
- MapGet 注册 GET 路由
- 参数自动从 query/route 绑定
- dotnet watch run 自动热重载

## 练习/任务
1. 建一个 web 项目，跑起来访问首页
2. 加一个 `/time` 接口返回当前时间
3. 加一个 `/sum?a=1&b=2` 返回两数之和
4. （选做）用 dotnet watch run 改代码看自动重启

## 今日刷题
- **LeetCode 104 二叉树最大深度（复习）**：递归。
- **LeetCode 110 平衡二叉树（复习）**：后序返回高度。

## 打卡清单
- [ ] 跑起第一个 ASP.NET Core 项目
- [ ] 会写 MapGet 路由
- [ ] 会从 query 读参数
- [ ] 已完成至少一道今日刷题
