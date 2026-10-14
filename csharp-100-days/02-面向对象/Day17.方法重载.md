# Day 17 · 方法重载

## 今日目标
- 理解方法重载的定义与价值
- 掌握重载的判断依据：参数列表不同
- 体会 .NET BCL 里到处都是重载

## 学习内容

### 什么是方法重载

同一个类里，**方法名相同但参数列表不同**（个数或类型不同），叫重载。调用时编译器根据你传的参数自动挑版本。

```csharp
class Printer
{
    public void Print(int n) => Console.WriteLine($"整数：{n}");
    public void Print(string s) => Console.WriteLine($"字符串：{s}");
    public void Print(int a, int b) => Console.WriteLine($"两数：{a} {b}");
}

Printer p = new Printer();
p.Print(123);          // 调用 int 版本
p.Print("hello");      // 调用 string 版本
p.Print(1, 2);         // 调用两参版本
```

### 重载看什么

| 能区分重载吗 | 例子 |
|---|---|
| 参数个数不同 | `Print(int)` vs `Print(int,int)` |
| 参数类型不同 | `Print(int)` vs `Print(string)` |
| **只改返回值不行** | `int F()` vs `void F()` 不算重载 |
| **只改参数名不行** | `Print(int a)` vs `Print(int x)` 是同一个方法 |

> 规则：**方法名相同，参数列表（个数/类型/顺序）不同**。返回值不参与重载判断。

### 构造函数也能重载

上一天已经见过：无参构造 + 有参构造就是重载。

### BCL 里的重载例子

```csharp
Console.WriteLine(123);
Console.WriteLine("abc");
Console.WriteLine(3.14);     // 同一个 WriteLine，传啥都行
```

`Console.WriteLine` 就是一大家族重载，这就是重载的价值——调用方不用记 `WriteInt`、`WriteString`。

### 可选参数 vs 重载

两种写法都能"传不同参数"，区别：

```csharp
// 可选参数（简单场景）
void Greet(string name, string greeting = "你好") => Console.WriteLine($"{greeting}，{name}");

// 重载（参数类型差别大时更清晰）
void Greet(string name) => Console.WriteLine($"你好，{name}");
void Greet(string name, int times) => Console.WriteLine($"你好 {times} 次，{name}");
```

实践中：参数类型差异大用重载，只是默认值差异用可选参数。

## 核心重点
- 方法重载：同名不同参（个数/类型/顺序）
- 返回值不同不算重载
- 重载让调用方用统一名字处理不同输入
- 构造函数也常重载
- BCL 大量用重载（如 Console.WriteLine）

## 练习/任务
1. 写一个 `Calculator` 类，重载 `Add` 方法：两个 int、三个 int、两个 double
2. 给 `Print` 类加重载：能打印 int、string、bool
3. 写 `Shape` 类，重载 `Area()`：无参返回 0，传半径算圆面积，传长宽算矩形面积
4. （选做）故意写两个仅返回值不同的同名方法，看编译器报什么错

## 打卡清单
- [ ] 能判断两个方法是不是重载
- [ ] 会写参数个数不同的重载
- [ ] 会写参数类型不同的重载
- [ ] 理解为什么返回值不算重载依据
