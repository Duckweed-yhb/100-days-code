# Day 52 · 程序集与托管执行

## 今日目标
- 理解程序集（Assembly）是什么
- 知道命名空间与程序集的区别
- 学会用 using 与全局 using

## 学习内容

### 程序集是什么

编译后输出的 `.dll` 或 `.exe` 就是一个**程序集**——.NET 部署与加载的最小单元。它包含：

- IL 代码
- 类型元数据（有哪些类、方法）
- 资源（图片、字符串）
- 版本号、公钥

### 命名空间 vs 程序集

| 概念 | 作用 | 例子 |
|---|---|---|
| 命名空间 | 逻辑上组织类，避免重名 | `System.Collections.Generic` |
| 程序集 | 物理文件，部署单元 | `System.Collections.dll` |

一个程序集可以包含多个命名空间；一个命名空间也可以跨多个程序集。

```csharp
using System.Collections.Generic;   // 引入命名空间，写 List<T> 不用全名

List<int> a = new();
```

### 全局 using

.NET 6+ 项目可以在 `GlobalUsings.cs` 或 csproj 里声明全局 using，所有文件共享：

```csharp
// GlobalUsings.cs
global using System;
global using System.Linq;
global using System.Collections.Generic;
```

现代控制台项目默认隐式 using 已包含常用命名空间。

### 引用程序集

要用别的项目或 NuGet 包的类，必须先"引用"它：

```powershell
dotnet add package Newtonsoft.Json
dotnet add reference ../OtherProject/OtherProject.csproj
```

没引用就用，编译报错"找不到类型"。

### 托管执行流程

1. 进程启动，加载 CLR；
2. CLR 加载需要的程序集；
3. JIT 把 IL 编译成本地码；
4. GC 自动管理内存；
5. 程序结束，CLR 卸载。

## 核心重点
- 程序集 = 编译产物 dll/exe，部署单元
- 命名空间是逻辑组织，程序集是物理文件
- using 引入命名空间，global using 全局共享
- 用别的类必须先引用对应程序集/包
- 托管代码由 CLR 加载、JIT、GC

## 练习/任务
1. 在项目里新建第二个类库项目，主项目引用它，调用其中一个类
2. 把常用 using 移到 GlobalUsings.cs
3. 查 `dotnet list package` 输出当前项目引用了哪些包
4. （选做）观察 bin/Debug/net8.0 下有哪些 dll

## 今日刷题
- **LeetCode 122 买卖股票最佳时机 II**：可多次交易。思路：贪心，今天比昨天涨就赚差价。
- **LeetCode 80 删除有序数组重复项 II**：最多留两个重复。思路：慢指针写位置，前两位保留，之后若与前两位不同才写。

## 打卡清单
- [ ] 理解程序集与命名空间区别
- [ ] 会用 using 与 global using
- [ ] 会引用类库项目与 NuGet 包
- [ ] 已完成至少一道今日刷题
