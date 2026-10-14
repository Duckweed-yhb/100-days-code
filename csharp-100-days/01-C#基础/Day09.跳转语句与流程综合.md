# Day 09 · 跳转语句与流程综合

## 今日目标
- 掌握 `break`、`continue`、`return` 的区别
- 能用循环 + 判断解决"找数、猜数字"类问题
- 写一个稍完整的控制台小程序

## 学习内容

### break：立即跳出整个循环

```csharp
// 找到第一个能被 7 整除的两位数就停
for (int i = 10; i < 100; i++)
{
    if (i % 7 == 0)
    {
        Console.WriteLine($"找到了：{i}");
        break;      // 跳出 for，不再继续
    }
}
```

### continue：跳过本次，继续下一轮

```csharp
// 打印 1~10 中所有不是 3 的倍数的数
for (int i = 1; i <= 10; i++)
{
    if (i % 3 == 0) continue;
    Console.WriteLine(i);
}
```

### 两者区别

| 语句 | 作用 |
|---|---|
| `break` | 整个循环立即结束 |
| `continue` | 跳过本次循环体剩下的语句，进入下一次 |
| `return` | 直接从方法返回（后面会学） |

### 综合例子：猜数字

程序随机想一个 1~100 的数，玩家猜，提示大了/小了，直到猜对：

```csharp
Random rnd = new Random();
int answer = rnd.Next(1, 101);     // 1 到 100
int guess;
int times = 0;

do
{
    Console.Write("猜一个 1~100 的数：");
    guess = int.Parse(Console.ReadLine()!);
    times++;

    if (guess > answer) Console.WriteLine("大了");
    else if (guess < answer) Console.WriteLine("小了");
} while (guess != answer);

Console.WriteLine($"恭喜猜对！共用了 {times} 次");
```

### 标签与 goto（了解即可）

不推荐滥用，但 break 只能跳出一层循环。C# 也提供 `goto` 跳到标签：

```csharp
for (int i = 0; i < 10; i++)
{
    for (int j = 0; j < 10; j++)
    {
        if (i * j > 50) goto End;
    }
}
End:
Console.WriteLine("结束");
```

> 一般用 `break` 加标志变量或把内层循环抽成方法即可，能不用 goto 就不用。

## 核心重点
- `break` 跳出整个循环，`continue` 跳过本次
- `Random.Next(1, 101)` 生成范围随机数
- do-while 特别适合"猜数字"这种至少问一次的场景
- 嵌套循环 break 只跳一层
- 控制流语句别滥用，写多了容易乱

## 练习/任务
1. 用 break 找出 100~200 之间第一个质数（提示：内层试除，找不到因子才是质数）
2. 用 continue 求 1~100 中所有不被 5 整除的数之和
3. 完善猜数字：限制最多猜 7 次，超过则宣布失败并公布答案
4. （选做）让玩家玩两轮，每轮结束问"再来一局吗"，输入 n 退出

## 打卡清单
- [ ] 能分清 break 与 continue
- [ ] 会用 Random 生成随机数
- [ ] 已写出可玩的猜数字游戏
- [ ] 理解嵌套循环中 break 只跳一层
