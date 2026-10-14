# Day 63 · 内存与IO阶段练习

## 今日目标
- 综合运用内存管理、文件 IO、正则、反射
- 写一个"日志分析器"小工具
- 复盘整个阶段

## 学习内容

### 本阶段回顾

| 天 | 主题 |
|---|---|
| 51-52 | .NET 平台、运行时、程序集 |
| 53-54 | 值/引用类型、栈与堆 |
| 55-56 | GC、IDisposable、内存泄漏 |
| 57 | StringBuilder |
| 58 | 正则 |
| 59-60 | 文件与流 |
| 61-62 | 反射、特性 |

### 示范：日志分析器

需求：读一个日志文件，统计每个级别（INFO/WARN/ERROR）的行数，提取所有错误行号与时间。

```csharp
using System.IO;
using System.Text.RegularExpressions;

string path = "app.log";
if (!File.Exists(path))
{
    Console.WriteLine("日志文件不存在");
    return;
}

var counts = new Dictionary<string, int>();
var errors = new List<string>();

var lineRegex = new Regex(@"^(?<time>\d{4}-\d{2}-\d{2}\s\d{2}:\d{2}:\d{2})\s+(?<level>INFO|WARN|ERROR)\s+(?<msg>.*)$");

using var reader = new StreamReader(path);
string? line;
while ((line = await reader.ReadLineAsync()) != null)
{
    Match m = lineRegex.Match(line);
    if (!m.Success) continue;

    string level = m.Groups["level"].Value;
    counts[level] = counts.TryGetValue(level, out int c) ? c + 1 : 1;

    if (level == "ERROR") errors.Add(line);
}

Console.WriteLine("=== 统计 ===");
foreach (var kv in counts) Console.WriteLine($"{kv.Key}: {kv.Value}");

Console.WriteLine($"\n=== ERROR 行（共 {errors.Count}）===");
foreach (var e in errors) Console.WriteLine(e);
```

### 设计要点

- 用 `StreamReader` 一行行读，不一次性进内存；
- 正则一次编译，反复用；
- 字典做计数；
- using 保证文件句柄释放。

## 核心重点
- 大文件用流、小文件用 ReadAllText
- 正则一次编译反复用
- IDisposable 必包 using
- 反射与特性是框架的玩具，业务代码少碰
- 这阶段结束，你懂 .NET 是怎么跑起来的

## 练习/任务
1. 实现上面的日志分析器，扩展支持 WARN
2. 把错误行单独写到 `errors.log`
3. 统计每个小时的错误数（按时间分组）
4. （选做）用反射写一个小工具：打印一个对象的所有属性 JSON 风格字符串

## 今日刷题
- **LeetCode 169 多数元素**：出现次数 > n/2。思路：Boyer-Moore 投票，O(1) 空间。
- **LeetCode 229 求众数 II**：出现次数 > n/3。思路：Boyer-Moore 两候选。

## 打卡清单
- [ ] 已完成日志分析器
- [ ] 会用 StreamReader 大文件
- [ ] 会用正则提取字段
- [ ] 已完成至少一道今日刷题
