# Day 51 · .NET平台与运行时

## 今日目标
- 理解 .NET 平台的组成：运行时、BCL、SDK
- 知道托管代码与 JIT 的关系
- 区分 .NET Framework / .NET Core / .NET 5+

## 学习内容

### .NET 平台由什么组成

```
.NET
 ├── .NET 运行时（CoreCLR）：执行托管代码、GC、JIT
 ├── 基类库 BCL：System.Console、System.IO、System.Linq...
 ├── SDK：编译器、dotnet CLI、模板
 └── 应用框架：ASP.NET Core、WinForms、WPF...
```

### 托管代码与 JIT

我们写的 C# 先编译成 IL（中间语言），打包进 `.dll`/`.exe`。运行时 CLR 启动后，JIT（即时编译器）把 IL 按需要编译成本地机器码执行。

- 与 C 比：C 一次编译成机器码；C# 运行时再 JIT；
- 与 Python 比：Python 逐行解释；C# 先编译成 IL 再 JIT，快得多。

### .NET 的历史

| 名称 | 年代 | 状态 |
|---|---|---|
| .NET Framework | 2002-2020 | 仅 Windows，已停更 |
| .NET Core | 2016-2020 | 跨平台，已被取代 |
| .NET 5+ | 2020 至今 | 统一平台，跨平台 |
| .NET 8 | 2024 | LTS，本路线默认 |
| .NET 9 | 2024 | STS，最新 |

> 看到老教程里说".NET Framework"，那是 Windows 专属老框架；现代项目都用 .NET 8/9。

### dotnet CLI 常用命令

```powershell
dotnet new console          # 新建控制台项目
dotnet new webapi           # 新建 Web API 项目
dotnet build                # 编译
dotnet run                  # 编译并运行
dotnet test                 # 跑测试
dotnet add package XXX      # 加 NuGet 包
dotnet publish              # 发布
```

### 一个程序集里有什么

编译出来的 `.dll` 包含：IL 代码、类型元数据、资源、版本号。.NET 用反射（Day61）在运行时读这些元数据。

## 核心重点
- .NET = 运行时 + BCL + SDK
- C# 编译成 IL，运行时 JIT 成本地码
- .NET 8 是当前 LTS，跨 Windows/Linux/macOS
- dotnet CLI 是日常工具
- 老教程里的 .NET Framework 别再用

## 练习/任务
1. 跑 `dotnet --info`，看本机 SDK 与运行时版本
2. 用 `dotnet new console` 与 `dotnet new classlib` 各建一个项目，观察 csproj 区别
3. 查 `dotnet publish` 输出的 dll 长什么样（用资源管理器看 bin 目录）
4. （选做）查 .NET 8 与 .NET 9 的区别（LTS vs STS）

## 今日刷题
- **LeetCode 119 杨辉三角 II**：滚动数组。思路：从后往前覆盖，每行由上一行推。
- **LeetCode 121 买卖股票最佳时机**：一次遍历，记录历史最低价，每天卖则 profit = price - min。

## 打卡清单
- [ ] 理解 .NET 平台组成
- [ ] 知道托管代码 JIT 流程
- [ ] 会用 dotnet CLI 基本命令
- [ ] 已完成至少一道今日刷题
