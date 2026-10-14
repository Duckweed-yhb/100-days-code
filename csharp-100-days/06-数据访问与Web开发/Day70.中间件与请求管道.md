# Day 70 · 中间件与请求管道

## 今日目标
- 理解中间件是什么、请求管道怎么流
- 会写自定义中间件
- 知道常用内置中间件的顺序

## 学习内容

### 中间件是什么

每个请求都要经过一串中间件：日志、异常处理、认证、路由……像流水线，一环扣一环。

```csharp
var builder = WebApplication.CreateBuilder(args);
var app = builder.Build();

// 自定义中间件
app.Use(async (context, next) =>
{
    Console.WriteLine($"进入：{context.Request.Method} {context.Request.Path}");
    await next();      // 交给下一个中间件
    Console.WriteLine($"离开：{context.Response.StatusCode}");
});

app.MapGet("/", () => "Hello");

app.Run();
```

### 顺序很重要

```csharp
app.UseExceptionHandler("/error");   // 异常处理要在最外
app.UseStaticFiles();                 // 静态文件
app.UseRouting();                     // 路由
app.UseAuthentication();              // 认证
app.UseAuthorization();               // 授权
app.MapControllers();                 // 端点
```

前面的先看到请求、后看到响应；后面的先处理请求、后看到响应。

### 常用内置中间件

| 中间件 | 作用 |
|---|---|
| `UseDeveloperExceptionPage` | 开发环境详细错误页 |
| `UseHttpsRedirection` | HTTP 跳转 HTTPS |
| `UseStaticFiles` | 提供 wwwroot 静态文件 |
| `UseRouting` | 路由 |
| `UseAuthentication` | 认证 |
| `UseCors` | 跨域 |

### MapWhen：按条件分支

```csharp
app.MapWhen(ctx => ctx.Request.Path.StartsWithSegments("/admin"), adminApp =>
{
    adminApp.Run(async ctx => await ctx.Response.WriteAsync("管理后台"));
});
```

## 核心重点
- 中间件是请求管道上的一环
- `await next()` 交给下一个
- 顺序重要：异常/认证在前，路由端点在后
- 每个请求都过所有中间件
- 中间件适合做横切关注点（日志、CORS、鉴权）

## 练习/任务
1. 写一个中间件打印每个请求的方法、路径、耗时
2. 写一个中间件：路径不是 /health 就要求带 token
3. 把 UseExceptionHandler 加到最外层，故意抛异常看效果
4. （选做）查 CORS 中间件怎么配

## 今日刷题
- **LeetCode 102 层序遍历（复习）**：Queue BFS。
- **LeetCode 103 锯齿层序（复习）**：BFS + 反转。

## 打卡清单
- [ ] 理解中间件管道
- [ ] 会写自定义中间件
- [ ] 知道常用中间件顺序
- [ ] 已完成至少一道今日刷题
