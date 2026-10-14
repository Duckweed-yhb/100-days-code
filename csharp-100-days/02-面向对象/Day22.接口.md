# Day 22 · 接口

## 今日目标
- 理解接口"能做什么"的约定本质
- 学会定义接口、类实现接口
- 体会接口与抽象类的区别

## 学习内容

### 接口是什么

接口只约定"有哪些方法/属性"，不写实现。类去**实现**接口，承诺自己能做这些事。

```csharp
interface IShape
{
    double Area();        // 接口方法默认 public abstract，不写也一样
    string Name { get; }
}

class Circle : IShape
{
    public double Radius { get; set; }
    public double Area() => Math.PI * Radius * Radius;   // 必须实现
    public string Name => "圆";
}
```

### 面向接口编程

```csharp
// 方法不在乎你是圆是矩形，只要你是 IShape
static void PrintArea(IShape s)
{
    Console.WriteLine($"{s.Name} 的面积是 {s.Area():F2}");
}

PrintArea(new Circle { Radius = 2 });
PrintArea(new Rectangle { Width = 3, Height = 4 });
```

新增一个三角形，只要它也实现 IShape，`PrintArea` 一行不改。这就是**解耦**。

### 接口 vs 抽象类

| 对比 | 抽象类 | 接口 |
|---|---|---|
| 能否实例化 | 不能 | 不能 |
| 字段 | 可以 | 不可以（只能定义属性签名） |
| 实现代码 | 可以有 | 默认无（C# 8 后可加默认实现，少用） |
| 类能继承几个 | 一个父类 | 可实现多个接口 |
| 表达关系 | is-a | can-do / 行为约定 |

> 经验逻辑："是什么"用抽象类，"能做什么"用接口。

### 常见 .NET 接口

```csharp
IComparable     // 可比较
IEnumerable     // 可遍历（集合都实现它）
IDisposable     // 可释放资源（Day56 讲）
```

## 核心重点
- 接口只约定行为，不写实现
- 类用 `class X : IShape` 实现接口，必须把成员全实现
- 面向接口编程，方法参数写接口类型而不是具体类
- 一个类可实现多个接口，只能继承一个类
- 接口是"can-do"，抽象类是"is-a"

## 练习/任务
1. 定义接口 `IPrintable`，含 `void Print()`；让 Book、Student 都实现它
2. 写方法 `PrintAll(IPrintable[] items)`，循环打印
3. 定义接口 `ISeries`：含 `int Next()`（返回下一个数）；实现 `EvenSeries`（偶数序列）与 `OddSeries`
4. （选做）让一个类同时实现 IPrintable 与 ISeries 两个接口

## 打卡清单
- [ ] 会定义接口并实现它
- [ ] 会用接口类型做方法参数
- [ ] 理解接口与抽象类的分工
- [ ] 知道一个类能实现多个接口
