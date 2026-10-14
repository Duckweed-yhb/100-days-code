# Day 25 · 面向对象阶段项目

## 今日目标
- 用面向对象思路独立设计一个小项目
- 综合运用类、继承、多态、接口、抽象类
- 完成一次"从需求到代码"的完整练习

## 学习内容

### 项目需求：图形面积与周长计算器

做一个控制台程序：

1. 支持三种图形：圆、矩形、三角形；
2. 用户输入图形类型与参数，程序输出面积与周长；
3. 能同时保存多个图形，最后统一打印一份报告；
4. 代码要新增图形时，主流程一行不改。

### 设计思路

- 抽象类 `Shape`：约定 `Area()`、`Perimeter()`、`Name`；
- 派生 `Circle`、`Rectangle`、`Triangle`；
- 主程序维护一个 `List<Shape>`，多态地遍历。

### 参考骨架

```csharp
abstract class Shape
{
    public abstract string Name { get; }
    public abstract double Area();
    public abstract double Perimeter();

    public void Print()
    {
        Console.WriteLine($"{Name,-8} 面积={Area(),F2} 周长={Perimeter(),F2}");
    }
}

class Circle(double r) : Shape
{
    public override string Name => "圆";
    public override double Area() => Math.PI * r * r;
    public override double Perimeter() => 2 * Math.PI * r;
}

class Rectangle(double w, double h) : Shape
{
    public override string Name => "矩形";
    public override double Area() => w * h;
    public override double Perimeter() => 2 * (w + h);
}

class Triangle(double a, double b, double c) : Shape
{
    public override string Name => "三角形";
    public override double Area()
    {
        double s = (a + b + c) / 2;
        return Math.Sqrt(s * (s - a) * (s - b) * (s - c));   // 海伦公式
    }
    public override double Perimeter() => a + b + c;
}
```

```csharp
List<Shape> shapes = new();

while (true)
{
    Console.Write("添加图形？(c=圆 r=矩形 t=三角 q=退出)：");
    string cmd = Console.ReadLine()!.Trim();
    if (cmd == "q") break;

    if (cmd == "c")
    {
        Console.Write("半径：");
        double r = double.Parse(Console.ReadLine()!);
        shapes.Add(new Circle(r));
    }
    // ... r、t 类似
}

Console.WriteLine("\n=== 报告 ===");
foreach (Shape s in shapes) s.Print();
```

### 评估自己写得对不对

- 主程序里有没有出现 `if (x is Circle)` 这种"按类型分支"？理想情况下尽量少——多态应该替你做分发；
- 加新图形（比如正五边形）时，主循环改了吗？应该只加一个类；
- 字段都封装好了吗？别再 `public 字段` 满天飞。

## 核心重点
- 抽象类定义"是什么 + 必须有什么行为"
- 派生类各自实现面积、周长
- 主程序面向 `List<Shape>` 编程，多态遍历
- 加新类型 = 加一个类，不动老代码
- 这就是"开闭原则"的最小实践

## 练习/任务
1. 实现上面的图形计算器，支持圆、矩形、三角形
2. 新增 `Square`（正方形）类，验证主程序无需修改即可加入
3. 给报告加一行：所有图形的总面积、总周长
4. （选做）加输入校验：三角形三边必须满足任意两边和大于第三边

## 今日刷题
- **LeetCode 225 用队列实现栈**：用两个队列模拟栈的 push/pop/top/empty。思路：push 时把元素塞进"主队列"；pop 时把前 n-1 个挪到备用队列，剩下那个就是栈顶。C# 用 `Queue<T>`（Day31 细讲）即可。
- **LeetCode 206 反转链表**：定义 `ListNode` 类（`Val`、`Next`），把单链表就地反转。思路：三个指针 prev/cur/next，循环把 cur.Next 指向 prev。这题是面向对象"节点类 + 指针操作"的经典入门。

## 打卡清单
- [ ] 已完成图形计算器并跑通
- [ ] 新增 Square 类时未修改主流程
- [ ] 所有字段通过属性封装
- [ ] 已完成至少一道今日刷题
