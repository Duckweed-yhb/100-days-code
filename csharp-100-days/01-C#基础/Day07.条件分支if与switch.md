# Day 07 · 条件分支if与switch

## 今日目标
- 熟练使用 if / else if / else 做多分支判断
- 掌握 switch 语句与 switch 表达式
- 理解嵌套条件与三元运算符

## 学习内容

### if / else

```csharp
int score = 85;

if (score >= 90)
{
    Console.WriteLine("优秀");
}
else if (score >= 80)
{
    Console.WriteLine("良好");
}
else if (score >= 60)
{
    Console.WriteLine("及格");
}
else
{
    Console.WriteLine("不及格");
}
```

注意：判断条件必须是 `bool`，不能写 `if (score)`（C 风格在 C# 里不合法）。

### 三元运算符

简单二选一可以写成一行：

```csharp
string result = score >= 60 ? "及格" : "不及格";
//              条件        为真     为假
```

### switch 语句

等值判断多时用 switch 更清晰：

```csharp
string day = "一";
switch (day)
{
    case "一":
    case "三":
    case "五":
        Console.WriteLine("今天有 C# 课");
        break;
    case "六":
    case "日":
        Console.WriteLine("周末复盘");
        break;
    default:
        Console.WriteLine("工作日");
        break;
}
```

C# 里每个 case 后必须 `break`（或 `goto` / 跳转），不会像 C 那样"穿透"，多个 case 写在一起表示"满足任一都执行"。

### switch 表达式（C# 8+，现代写法）

把 switch 当表达式赋值：

```csharp
string level = score switch
{
    >= 90 => "优秀",
    >= 80 => "良好",
    >= 60 => "及格",
    _     => "不及格"      // _ 是默认分支
};
Console.WriteLine(level);
```

这种写法简洁，配合后面的模式匹配会越来越强。

### 嵌套与建议

```csharp
if (age >= 18)
{
    if (hasTicket)
        Console.WriteLine("入场");
    else
        Console.WriteLine("请购票");
}
else
{
    Console.WriteLine("未成年人需家长陪同");
}
```

> 实践建议：分支超过 3 层就要考虑拆成方法或用 switch 表达式，别把逻辑层层套娃。

## 核心重点
- if 条件必须是 bool；else if 顺序判断，命中即跳出
- `条件 ? 值1 : 值2` 三元表达式
- switch 语句每个 case 要 break，多 case 合并即"或"
- switch 表达式用 `=>` 与 `_` 写默认分支，更简洁
- 分支别嵌套太深，及时拆方法

## 练习/任务
1. 输入百分制成绩，用 if-else if 输出等级（A/B/C/D/E）
2. 用 switch 表达式重写上面的等级判断
3. 输入月份数字，输出该月天数（平年 2 月按 28 天）
4. （选做）写一个简易 BMI 分类器：输入身高体重，输出偏瘦/正常/超重/肥胖

## 打卡清单
- [ ] 会写 if-else if-else 多分支
- [ ] 会用三元运算符做简单二选一
- [ ] 会用 switch 表达式给变量赋值
- [ ] 已写过 BMI 判断小程序
