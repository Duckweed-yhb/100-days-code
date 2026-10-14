# Day 21 · 抽象类

## 今日目标
- 理解什么时候用抽象类
- 掌握 `abstract` 类与 `abstract` 方法
- 区分抽象类与普通类、接口

## 学习内容

### 抽象类是什么

有些基类"本身就不该被实例化"，比如 Shape（形状）——你不会 new 一个"形状"出来，只会 new 圆、矩形。这种基类用 `abstract` 修饰：

```csharp
abstract class Shape
{
    public string Name { get; set; } = "";

    // 抽象方法：只有声明，没有实现，强制子类去写
    public abstract double Area();

    // 普通方法：可以有实现
    public void Print()
    {
        Console.WriteLine($"{Name}，面积 {Area():F2}");
    }
}
```

### 子类必须实现抽象方法

```csharp
class Circle : Shape
{
    public double Radius { get; set; }

    public override double Area() => Math.PI * Radius * Radius;
}

class Rectangle : Shape
{
    public double Width { get; set; }
    public double Height { get; set; }

    public override double Area() => Width * Height;
}
```

```csharp
// Shape s = new Shape();    // 错：抽象类不能 new
Shape c = new Circle { Name = "圆", Radius = 2 };
Shape r = new Rectangle { Name = "矩形", Width = 3, Height = 4 };

c.Print();     // 圆，面积 12.57
r.Print();     // 矩形，面积 12.00
```

### 抽象类特点

| 特点 | 说明 |
|---|---|
| 不能 new | 只能做基类 |
| 可含抽象方法 | 子类必须 override |
| 可含普通方法/字段 | 与普通类一样 |
| 构造函数可写 | 供子类 base 调用 |

### 抽象类 vs 普通基类

- 普通基类：可以 new，方法有默认实现；
- 抽象类：不能 new，强制子类实现某些方法。

当你想"约束子类必须有某个行为，但又没法在基类给出统一实现"时，用抽象类。

## 核心重点
- `abstract class` 不能实例化，专门当基类
- `abstract` 方法只有签名，子类必须 `override`
- 抽象类里也能写普通方法和字段
- 抽象类提供"模板 + 强制约定"
- 与接口区别：抽象类是"is-a + 部分实现"，接口是"能做什么"

## 练习/任务
1. 把上一天的 Animal 改成抽象类，`Speak()` 改成抽象方法
2. 写抽象类 `Employee`（Name、Salary），抽象方法 `CalculateBonus()`；派生 `Manager`（奖金=工资20%）与 `Intern`（固定 500）
3. 用 `Employee[]` 装不同员工，循环打印奖金
4. （选做）给抽象类加一个实现好的方法 `Introduce()`，观察子类是否自动继承

## 打卡清单
- [ ] 理解抽象类不能 new
- [ ] 会写 abstract 方法并在子类 override
- [ ] 会在抽象类里写普通方法复用
- [ ] 能说出抽象类存在的意义
