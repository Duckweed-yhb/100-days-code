# Day 02 · 第一个C#程序与程序结构

## 今日目标
- 看懂一个 C# 控制台项目里每个文件是干嘛的
- 理解顶级语句与传统 Main 方法两种写法
- 掌握 `Console` 输入输出的基本用法

## 学习内容

### 控制台项目的文件结构

`dotnet new console` 生成的项目里关键文件：

| 文件 | 作用 |
|---|---|
| `Program.cs` | 源代码，程序入口所在 |
| `HelloWorld.csproj` | 项目文件，声明目标框架、依赖包 |
| `obj/`、`bin/` | 中间产物与编译输出，不用手改 |

`.csproj` 大致长这样：

```xml
<Project Sdk="Microsoft.NET.Sdk">
  <PropertyGroup>
    <OutputType>Exe</OutputType>
    <TargetFramework>net8.0</TargetFramework>
    <Nullable>enable</Nullable>
  </PropertyGroup>
</Project>
```

### 入口：顶级语句 vs Main 方法

C# 9 起支持**顶级语句**，把入口函数隐藏，直接写代码：

```csharp
Console.WriteLine("Hello");
```

传统写法（了解即可，老项目里常见）：

```csharp
using System;                       // 引入命名空间
namespace HelloWorld                // 命名空间，组织代码
{
    class Program                   // 类
    {
        static void Main()          // 入口方法
        {
            Console.WriteLine("Hello");
        }
    }
}
```

现代 C# 推荐用顶级语句，少写样板代码。

### 控制台输入输出

```csharp
Console.Write("你叫什么名字？");          // 不换行
string name = Console.ReadLine();          // 读一行用户输入
Console.WriteLine($"你好，{name}！");       // 字符串插值输出

int age = 18;
Console.WriteLine($"明年你 {age + 1} 岁");  // 花括号里可以写表达式
```

注意：`Console.ReadLine()` 返回 `string`，即使输入的是数字也要先转换（明天讲）。

### 注释与分号

```csharp
// 单行注释：双斜杠开头

/* 多行注释
   可以写好几行 */

/// <summary>
/// XML 文档注释，写在方法/类上方，IDE 会提示
/// </summary>
```

- 每条语句以英文分号 `;` 结尾；
- 大括号 `{ }` 缩进统一（VS Code 默认 4 空格）；
- 区分大小写：`Console` 不能写成 `console`。

## 核心重点
- `Program.cs` 是入口；`.csproj` 描述项目；`bin/` 是编译输出
- 顶级语句是现代写法；传统 `Main` 写法要能看懂
- `Console.WriteLine` 输出并换行，`Console.Write` 不换行，`ReadLine` 读一行
- `$"..."` 字符串插值在花括号里嵌表达式
- C# 区分大小写，语句以分号结尾

## 练习/任务
1. 写程序：提示用户输入姓名，再输出"欢迎你，XX"
2. 写程序：连续读两个数（先按字符串读），原样拼接输出，观察和你预期是否一样
3. 把顶级语句版本改写成传统 `class Program + Main` 版本，运行结果应一致

## 打卡清单
- [ ] 能说清项目里 Program.cs 与 .csproj 的作用
- [ ] 已用 ReadLine 读取用户输入并输出
- [ ] 已使用 $ 插值字符串拼接变量
- [ ] 能看懂传统 Main 写法的程序结构
