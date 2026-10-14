# Day 38 · 委托delegate

## 今日目标
- 理解委托是"方法的类型"
- 学会声明、实例化、调用委托
- 知道委托为什么是事件与 Lambda 的基础

## 学习内容

### 委托是什么

前面我们把 int、string 当参数传。现在要把**方法本身**当参数传，怎么办？用委托。

委托是一种类型，它"装"的是方法：

```csharp
// 声明一个委托类型：能装"接收两个 int 返回 int"的方法
delegate int BinaryOp(int a, int b);

static int Add(int a, int b) => a + b;
static int Mul(int a, int b) => a * b;

BinaryOp op = Add;          // 把方法赋值给委托
Console.WriteLine(op(3, 5)); // 调用委托 → 8

op = Mul;
Console.WriteLine(op(3, 5)); // 15
```

### 委托当参数

```csharp
// 计算器：传什么运算就做什么运算
static int Compute(int a, int b, BinaryOp op) => op(a, b);

Console.WriteLine(Compute(10, 3, Add));   // 13
Console.WriteLine(Compute(10, 3, Mul));   // 30
```

这就是"把算法当参数"——策略模式的雏形。

### 多播委托（链式调用）

一个委托可以挂多个方法，`+` 挂上、`-` 摘下：

```csharp
Action<string> logger = Console.WriteLine;
logger += msg => File.AppendAllText("log.txt", msg + "\n");

logger("系统启动");      // 既打印到屏幕，又写文件
```

`Action<string>` 是 .NET 内置委托，明天细讲。

### 为什么需要委托

- 回调：做完事后通知调用方；
- 事件：按钮点击、数据到达；
- LINQ：Where/Select 里传的就是委托；
- 策略：运行时换算法。

## 核心重点
- 委托是"方法的类型"，把方法当数据传
- `delegate int BinaryOp(int a, int b);` 声明
- 委托变量可以指向不同方法，运行时换行为
- `+/-` 多播挂多个方法
- 事件、Lambda、LINQ 都建立在委托之上

## 练习/任务
1. 声明委托 `int Transformer(int x)`，写方法 `Map` 对数组每个元素应用该变换
2. 写方法 `Filter(int[] a, Predicate<int> p)`，保留满足条件的元素（Predicate 也是委托）
3. 用多播委托让一个通知同时打印屏幕、写文件
4. （选做）把 Day35 的 LINQ Where 用委托手写一遍

## 今日刷题
- **LeetCode 234 回文链表**：判断单链表是否回文。思路：找中点 + 反转后半 + 比较。今天重点是用委托思想把"比较两个节点"抽成方法。
- **LeetCode 160 相交链表**：找两个链表交点。思路：两个指针各走各的，走到头后换到对方头继续，相遇点即交点。

## 打卡清单
- [ ] 理解委托是方法的类型
- [ ] 会声明并使用自定义委托
- [ ] 会把委托当参数传
- [ ] 已完成至少一道今日刷题
