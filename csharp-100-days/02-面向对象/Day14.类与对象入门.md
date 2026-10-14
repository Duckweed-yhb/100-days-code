# Day 14 · 类与对象入门

## 今日目标
- 理解"类是图纸，对象是产品"
- 学会定义最简单的类、创建对象、访问成员
- 用类把相关数据和行为打包到一起

## 学习内容

### 为什么需要面向对象

前面我们用数组、方法写程序，数据和操作是分开的。现实世界里"学生"既有姓名、年龄等**数据**，又有学习、考试等**行为**。面向对象（OOP）就是把它们绑成一个整体——**类**。

### 定义一个类

```csharp
class Student
{
    // 字段：数据
    public string Name = "";
    public int Age;

    // 方法：行为
    public void Introduce()
    {
        Console.WriteLine($"我叫 {Name}，今年 {Age} 岁");
    }
}
```

### 创建对象（实例化）

```csharp
Student s1 = new Student();
s1.Name = "余泓彬";
s1.Age = 20;
s1.Introduce();        // 我叫 余泓彬，今年 20 岁

Student s2 = new Student();
s2.Name = "张三";
s2.Age = 21;
s2.Introduce();
```

> 类是图纸，`new` 出来的 `s1`、`s2` 是两张按图纸造出来的独立产品，各自有自己的字段。

### 字段、方法、访问

- **字段（field）**：类里存的数据，如 `Name`、`Age`；
- **方法（method）**：类里的行为，如 `Introduce()`；
- **`public`**：公开，外面能访问；后面会讲为什么不直接 public 字段。

### 在一个文件里写多个类

```csharp
// Program.cs
Student alice = new Student { Name = "Alice", Age = 19 };
alice.Introduce();

class Student
{
    public string Name = "";
    public int Age;
    public void Introduce() => Console.WriteLine($"我叫 {Name}，{Age} 岁");
}
```

对象初始化器 `new Student { Name=..., Age=... }` 可以在创建时一次性赋值。

### 类与结构体初步

C# 里 class 是**引用类型**（对象在堆上，变量存引用），struct 是**值类型**。今天先知道有这回事，Day53 会细讲。

## 核心重点
- 类把数据（字段）和行为（方法）打包
- `new 类名()` 创建对象，每个对象字段独立
- 对象用 `.` 访问成员：`s.Name`、`s.Introduce()`
- 对象初始化器 `new Student { ... }` 一次性设字段
- class 是引用类型

## 练习/任务
1. 设计一个 `Book` 类：字段有书名、作者、价格；方法 `Show()` 打印信息。创建 3 本书并打印
2. 设计一个 `Dog` 类：字段名字、品种；方法 `Bark()` 打印"汪汪，我是XX"
3. 给 `Student` 类加方法 `IsAdult()`，返回是否成年（Age>=18）
4. （选做）设计 `Rectangle` 类：字段宽高，方法 `Area()` 返回面积，打印两个矩形的面积

## 打卡清单
- [ ] 理解类是图纸、对象是实例
- [ ] 会定义类、new 对象、访问字段方法
- [ ] 会用对象初始化器
- [ ] 已写过 Book/Dog 这样的简单类
