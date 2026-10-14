# Day 08 · 循环for与while

## 今日目标
- 掌握 for、while、do-while 三种循环
- 能用循环累加、计数、嵌套打印图形
- 理解循环条件为什么容易死循环

## 学习内容

### for 循环

知道循环次数时用 for：

```csharp
// 打印 1 到 5
for (int i = 1; i <= 5; i++)
{
    Console.WriteLine(i);
}
```

三段：`初始化; 条件; 每次后做`。先初始化一次，再判断条件，成立就执行循环体，最后执行 `i++`。

### while 循环

不知道次数、靠条件退出时用 while：

```csharp
int n = 1;
while (n <= 100)
{
    n *= 2;                 // 1,2,4,8,...
}
Console.WriteLine($"翻了 {n} 才超过 100");
```

### do-while

先执行一次再判断，至少跑一次：

```csharp
string input;
do
{
    Console.Write("说点什么（quit 退出）：");
    input = Console.ReadLine()!;
    Console.WriteLine($"你说了：{input}");
} while (input != "quit");
```

### 嵌套循环

打印乘法表：

```csharp
for (int i = 1; i <= 9; i++)
{
    for (int j = 1; j <= i; j++)
    {
        Console.Write($"{j}×{i}={i * j}\t");
    }
    Console.WriteLine();
}
```

`\t` 是制表符对齐。

### 常见死循环

```csharp
// 错例：条件永远成立
while (true)
{
    Console.WriteLine("停不下来");
}

// 错例：忘了更新计数器
int i = 0;
while (i < 10)
{
    Console.WriteLine(i);
    // 忘了 i++
}
```

死循环按 `Ctrl+C` 停。

## 核心重点
- for 知道次数；while 看条件；do-while 至少跑一次
- 循环三要素：初始值、继续条件、每次更新计数器
- 嵌套循环外层走一步、内层走一圈
- 写循环先在脑子里代入小数字跑两遍
- 小心忘了更新计数器导致死循环

## 练习/任务
1. 用 for 求 1+2+...+100 的和
2. 用 while 不断读用户输入，输入 0 时停止，最后输出所有数的平均值
3. 打印九九乘法表（上三角形式）
4. （选做）打印 100 以内所有偶数，每行 10 个，用 \t 对齐

## 打卡清单
- [ ] 会用 for 累加求和
- [ ] 会用 do-while 写"至少问一次"的交互
- [ ] 会写嵌套循环打印乘法表
- [ ] 能解释死循环是怎么来的
