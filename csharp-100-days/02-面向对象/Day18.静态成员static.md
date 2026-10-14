# Day 18 · 静态成员static

## 今日目标
- 理解 static 修饰字段、方法、类的含义
- 区分实例成员与静态成员
- 知道什么时候该用静态、什么时候该用实例

## 学习内容

### 实例成员 vs 静态成员

前面我们写的字段和方法都属于"每个对象一份"。有时某个数据是**全类共享**的，比如统计创建了多少个 Student：

```csharp
class Student
{
    public string Name;
    public static int Count = 0;       // 全类共享，所有对象共用一份

    public Student(string name)
    {
        Name = name;
        Count++;                        // 每 new 一个就 +1
    }
}

Student a = new Student("A");
Student b = new Student("B");
Student c = new Student("C");
Console.WriteLine(Student.Count);      // 3，用类名访问，不是用对象
```

### 静态方法

静态方法不依赖对象，通过类名直接调：

```csharp
class MathUtil
{
    public static double Square(double x) => x * x;
    public static double CircleArea(double r) => Math.PI * r * r;
}

Console.WriteLine(MathUtil.Square(5));       // 25
Console.WriteLine(MathUtil.CircleArea(1));    // 3.14159...
```

> 注意：静态方法里**不能直接访问实例字段**（因为没有 this）。

### 静态类

如果一个类全是静态方法，干脆声明成静态类，防止被 new：

```csharp
static class StringUtil
{
    public static string Reverse(string s)
    {
        char[] arr = s.ToCharArray();
        Array.Reverse(arr);
        return new string(arr);
    }
}

Console.WriteLine(StringUtil.Reverse("abc"));   // cba
```

### 什么时候用静态

| 场景 | 建议 |
|---|---|
| 工具方法（无状态） | 静态类 + 静态方法，如 Math、StringUtil |
| 全局计数器、配置 | 静态字段 |
| 需要多实例、有状态、要被替换/测试 | 用实例（后面 DI 会强调） |
| 单元测试 mock 友好 | 尽量实例化，别滥用静态 |

### Main 方法为什么是 static

```csharp
static void Main() { }
```

因为程序启动时还没有任何对象，必须由运行时直接通过类名调用入口。

## 核心重点
- `static` 成员属于类，全类共享一份
- 用 `类名.成员` 访问，不需要 new
- 静态方法里不能访问实例成员
- 静态类不能被实例化，只装静态工具方法
- 别滥用静态：有状态、要替换的东西做成实例

## 练习/任务
1. 给 Student 类加静态字段 Count 与静态方法 GetCount()，统计创建对象数
2. 写静态工具类 `Calc`，含静态方法 `IsEven`、`IsOdd`、`Abs`
3. 写静态类 `IdGenerator`，每次调用 `Next()` 返回递增的 id（1,2,3...）
4. （选做）在静态方法里试着访问一个实例字段，看编译报错

## 打卡清单
- [ ] 理解静态字段全类共享
- [ ] 会写静态方法并用类名调用
- [ ] 知道静态方法不能访问实例成员
- [ ] 能判断什么时候该用静态
