# Day 13 · 基础综合练习

## 今日目标
- 把前 12 天的语法点串起来
- 独立完成一个稍完整的控制台小程序
- 复盘第一阶段，找出薄弱点

## 学习内容

### 本周/本阶段回顾

这 12 天我们学了：

| 模块 | 关键内容 |
|---|---|
| 环境与程序结构 | dotnet CLI、Program.cs、Main/顶级语句 |
| 变量类型 | 值类型、var、const、decimal |
| 转换 | 强转、Parse/TryParse、插值格式化 |
| 运算符 | 算术、比较、逻辑、自增 |
| 流程控制 | if/switch、for/while、break/continue |
| 方法 | 参数传递、ref/out/params、可选参数 |
| 数组与字符串 | 遍历、Sort、Split/Join、不可变 |

### 示范项目：学生成绩管理小系统

需求：录入 N 个学生姓名与三门课成绩，输出每科平均分、总分排名、不及格名单。

```csharp
Console.Write("请输入学生人数：");
int n = int.Parse(Console.ReadLine()!);

string[] names = new string[n];
double[,] scores = new double[n, 3];   // 每人三门课

for (int i = 0; i < n; i++)
{
    Console.Write($"第 {i + 1} 个学生姓名：");
    names[i] = Console.ReadLine()!;

    for (int j = 0; j < 3; j++)
    {
        Console.Write($"  第 {j + 1} 门成绩：");
        scores[i, j] = double.Parse(Console.ReadLine()!);
    }
}

// 每科平均分
for (int j = 0; j < 3; j++)
{
    double sum = 0;
    for (int i = 0; i < n; i++) sum += scores[i, j];
    Console.WriteLine($"第 {j + 1} 科平均：{sum / n:F2}");
}

// 不及格名单
Console.WriteLine("不及格学生（任一科 < 60）：");
for (int i = 0; i < n; i++)
{
    bool failed = false;
    for (int j = 0; j < 3; j++)
        if (scores[i, j] < 60) failed = true;
    if (failed) Console.WriteLine("  " + names[i]);
}
```

### 写综合程序的一般步骤

1. 先把需求拆成输入、处理、输出三块；
2. 想清楚用什么数据结构（今天用数组）；
3. 先用小数据（2~3 个学生）跑通，再放大；
4. 把重复逻辑抽成方法，主程序才清晰。

## 核心重点
- 综合程序 = 变量 + 流程 + 方法 + 数组的组合拳
- 二维数组 `double[n,3]` 适合"每人多科"这类表
- 写之前先在纸上列输入输出，别一上来就写代码
- 遇到报错先读错误信息，定位行号
- 这阶段结束后，你已具备写小程序的基本语法

## 练习/任务
1. 实现上面的学生成绩系统，加一个"按总分从高到低排序输出"
2. 写一个简易计算器：读两个数和一个运算符（+ - * /），输出结果；除零时提示错误
3. 写"九九乘法表 + 质数表"两个小工具，合并到一个菜单里：用户输入 1 看乘法表，2 看质数，3 退出
4. （选做）给计算器加历史记录：把每次计算存进字符串数组，退出时打印

## 打卡清单
- [ ] 已跑通学生成绩管理小程序
- [ ] 已写出支持 + - * / 的命令行计算器
- [ ] 能把一个需求拆成输入/处理/输出三段
- [ ] 已复盘本阶段，列出 3 个还不熟的点
