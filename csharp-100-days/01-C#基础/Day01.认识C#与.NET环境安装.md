# Day 01 · 认识C#与.NET环境安装

## 今日目标
- 搞清 C# 与 .NET 是什么关系，为什么今天还要学
- 在本机装好 .NET 8 SDK 与 VS Code 开发环境
- 跑通第一个 C# 程序 "Hello, World"，理解"编译 → 运行"流程

## 学习内容

### C# 与 .NET 是什么

C#（读作 C Sharp）由 Anders Hejlsberg 于 2000 年在微软主持设计，是一门**静态类型、类型安全、面向对象**的现代编程语言。.NET 是它赖以运行的开发平台与运行时，提供基础类库（BCL）、垃圾回收、JIT 编译等基础设施。

> 一句话区分：**C# 是语言，.NET 是平台**。同一份 C# 代码跑在 .NET 上；.NET 上也可以跑 F#、VB.NET。

| 概念 | 说明 |
|---|---|
| .NET SDK | 开发套件，含编译器 `csc`、运行时与 `dotnet` CLI |
| .NET 运行时 | 执行托管代码的引擎，Windows/Linux/macOS 都能跑 |
| .NET 8 | 长期支持版本（LTS），本路线默认描述对象 |
| C# 12 | 与 .NET 8 配套的语言版本 |

为什么学 C#：语法现代优雅、ASP.NET Core 性能顶尖、Unity 游戏生态、云原生与桌面客户端通吃。

### 安装开发环境（Windows）

1. 到 .NET 官网下载 **.NET 8.0 SDK**（选 SDK 而非 Runtime，因为要开发）；
2. 一路下一步安装；
3. 安装 [VS Code](https://code.visualstudio.com/)，并安装扩展 **C# Dev Kit**（微软官方，含语法高亮、调试、项目管理）；
4. （可选但推荐）安装 Visual Studio 2022 Community，勾选".NET 桌面开发"与"ASP.NET 和 Web 开发"工作负载。

验证安装（PowerShell）：

```powershell
dotnet --version
```

能输出版本号（如 `8.0.xxx`）即成功。

### 第一个 C# 程序

新建控制台项目（PowerShell 任选一个空目录执行）：

```powershell
dotnet new console -o HelloWorld
cd HelloWorld
```

打开自动生成的 `Program.cs`：

```csharp
// 每个 C# 程序都需要一个入口：顶级语句下 Main 被隐式隐藏
Console.WriteLine("Hello, World!");   // 打印一行文本并换行
```

运行：

```powershell
dotnet run
```

屏幕输出 `Hello, World!` 即成功。

### 理解"编译 → 运行"

C# 源码（`.cs`）先被编译成中间语言（IL），打包进程序集（`.dll` 或 `.exe`）；运行时由 .NET 运行时的 JIT（即时编译器）把 IL 成本地机器码执行。这与 C 的"一次编译成机器码"不同，但比 Python"逐行解释"快得多，兼顾开发效率与运行性能。

## 核心重点
- C# 是语言，.NET 是平台；本路线基于 .NET 8 / C# 12
- 安装 .NET 8 SDK 后用 `dotnet --version` 验证
- `dotnet new console` 建项目，`dotnet run` 编译并运行
- `Console.WriteLine` 输出一行文本；C# 语句以分号结尾
- C# 先编译成 IL，运行时 JIT 成本地机器码

## 练习/任务
1. 安装 .NET 8 SDK，执行 `dotnet --version` 确认版本号
2. 用 `dotnet new console` 建项目并 `dotnet run` 看到 `Hello, World!`
3. 修改 `Program.cs`，让它再打印一行你自己的名字
4. （选做）把 `WriteLine` 改成 `Write` 运行两次，观察换行差异

## 打卡清单
- [ ] 已安装 .NET 8 SDK，`dotnet --version` 有输出
- [ ] 已创建并运行第一个 C# 控制台项目
- [ ] 已修改输出内容并重新 `dotnet run`
- [ ] 已理解 C# "编译成 IL → JIT 运行"的基本流程
