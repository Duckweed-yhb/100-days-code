# Day 86 · NuGet包管理

## 今日目标
- 理解 NuGet 是 .NET 的包管理器
- 会加、列、删包
- 知道怎么挑靠谱的包

## 学习内容

### NuGet 是什么

NuGet 相当于 npm（Node）、pip（Python）：.NET 的第三方库仓库。`dotnet add package` 从 nuget.org 拉包。

### 常用命令

```powershell
dotnet add package Newtonsoft.Json      # 加包
dotnet list package                     # 列当前项目包
dotnet remove package Newtonsoft.Json    # 删包
dotnet restore                          # 还原（一般自动）
```

### csproj 里长这样

```xml
<ItemGroup>
  <PackageReference Include="Newtonsoft.Json" Version="13.0.3" />
</ItemGroup>
```

### 挑包看什么

| 指标 | 说明 |
|---|---|
| 下载量 | 高=用的人多 |
| 更新频率 | 最近还在维护 |
| 作者 | 官方/知名组织优先 |
| 依赖数 | 别引一堆间接依赖 |
| License | 开源协议兼容 |

### 常见好用包

| 包 | 用途 |
|---|---|
| Microsoft.EntityFrameworkCore.Sqlite | EF Core SQLite |
| Swashbuckle.AspNetCore | Swagger |
| Serilog.AspNetCore | 结构化日志 |
| AutoMapper | 对象映射 |
| FluentValidation | 流畅校验 |

> 别什么包都引，每个包都是一份依赖。

### 全局工具

```powershell
dotnet tool install --global dotnet-ef
dotnet tool install --global dotnet-outdated-tool
```

## 核心重点
- NuGet = .NET 包仓库
- dotnet add/list/remove package
- csproj 里 PackageReference
- 挑包看下载量、维护、作者
- 别滥引包

## 练习/任务
1. 给项目加一个 Serilog.AspNetCore，跑起来看日志
2. 用 `dotnet list package` 看当前依赖
3. 找一个 JSON 序列化库（System.Text.Json 是内置的）
4. （选做）查 NuGet.org 上某个包的依赖树

## 今日刷题
- **LeetCode 141 环形链表（复习）**。
- **LeetCode 142 环形链表 II（复习）**。

## 打卡清单
- [ ] 会加删 NuGet 包
- [ ] 会看 csproj
- [ ] 知道怎么挑包
- [ ] 已完成至少一道今日刷题
