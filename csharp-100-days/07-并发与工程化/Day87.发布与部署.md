# Day 87 · 发布与部署

## 今日目标
- 用 `dotnet publish` 发布应用
- 理解框架依赖 vs 独立部署
- 知道生产部署的基本思路

## 学习内容

### dotnet publish

```powershell
dotnet publish -c Release -o publish
```

`publish/` 目录里就是部署产物：dll、配置、运行时（视模式）。

### 两种部署模式

| 模式 | 说明 | 产物大小 |
|---|---|---|
| 框架依赖 (framework-dependent) | 目标机要装 .NET 运行时 | 小 |
| 独立 (self-contained) | 把运行时打包进去 | 大，但目标机不用装 |

```powershell
# 框架依赖
dotnet publish -c Release -o publish

# 独立部署（Windows 64 位）
dotnet publish -c Release -r win-x64 --self-contained -o publish
```

### 环境变量

生产用环境变量覆盖配置：

```powershell
$env:ASPNETCORE_ENVIRONMENT="Production"
$env:ConnectionStrings__Default="Server=prod.db"
dotnet run
```

`__` 表示配置层级。

### 部署方式概览

- **Windows**：IIS 托管；
- **Linux**：systemd + Kestrel，或 Nginx 反代；
- **容器**：打 Docker 镜像推到 K8s；
- **云**：Azure App Service、AWS ECS。

### 健康检查

```csharp
builder.Services.AddHealthChecks();
// ...
app.MapHealthChecks("/health");
```

K8s/负载均衡器靠 `/health` 判断服务活着没。

## 核心重点
- dotnet publish 出发布产物
- 框架依赖 vs 独立部署
- 环境变量覆盖配置
- 生产设 ASPNETCORE_ENVIRONMENT=Production
- 加 /health 健康检查

## 练习/任务
1. 把昨天的 Web API publish 出来
2. 在 publish 目录跑 dll，访问 swagger
3. 加 /health 端点
4. （选做）查 Dockerfile 怎么写 .NET 应用镜像

## 今日刷题
- **LeetCode 102 层序遍历（复习）**。
- **LeetCode 103 锯齿层序（复习）**。

## 打卡清单
- [ ] 会 dotnet publish
- [ ] 理解两种部署模式
- [ ] 会用环境变量配生产
- [ ] 已完成至少一道今日刷题
