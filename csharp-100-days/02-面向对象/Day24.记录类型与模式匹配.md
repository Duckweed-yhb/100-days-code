# Day 24 · 记录类型与模式匹配

## 今日目标
- 认识 `record` 这种专用于数据载体的类型
- 理解值相等与引用相等的区别
- 学会用简单模式匹配表达式

## 学习内容

### record 是什么

C# 9 引入 `record`，专门装"纯数据"。它自动带构造、相等比较、ToString，比 class 省事：

```csharp
record Point(int X, int Y);      // 一行声明一个 record！

Point p1 = new(1, 2);
Point p2 = new(1, 2);
Console.WriteLine(p1 == p2);     // True！按值相等
Console.WriteLine(p1);          // Point { X = 1, Y = 2 }
```

普通 class 用 `==` 比的是引用（是否同一个对象），record 默认按字段值比。

### record class vs record struct

```csharp
record class Student(string Name, int Age);    // 引用类型 record
record struct Point2D(int X, int Y);           // 值类型 record
```

### with 表达式：复制改一部分

```csharp
Point origin = new(0, 0);
Point moved = origin with { X = 5 };      // 复制一份，只改 X
Console.WriteLine(moved);                  // Point { X = 5, Y = 0 }
```

### 何时用 record vs class

| 场景 | 选 |
|---|---|
| 纯数据传输、DTO、不可变模型 | record |
| 有行为、要继承、有可变状态 | class |
| 性能敏感的小对象（栈分配） | record struct / struct |

### 简单模式匹配回顾

```csharp
object o = "hello";

if (o is string s) Console.WriteLine($"字符串长度 {s.Length}");
else if (o is int i) Console.WriteLine($"整数 {i}");

// switch 表达式模式匹配
string Describe(object x) => x switch
{
    0 => "零",
    int n when n > 0 => "正整数",
    string s => $"字符串 {s}",
    null => "空",
    _ => "其他"
};
```

## 核心重点
- `record` 一行声明数据载体，自动有值相等与 ToString
- record 默认按字段值比较，class 默认按引用
- `with { ... }` 复制一份并改部分字段
- DTO、不可变模型用 record；有行为、有状态用 class
- `is` 类型模式 + `switch` 表达式模式匹配

## 练习/任务
1. 用 record 定义 `Product(string Name, decimal Price, string Category)`，创建两个相同产品，验证 `==` 为 true
2. 用 `with` 基于一个 Product 复制出"降价版"
3. 写一个 `Describe` 方法用 switch 模式匹配处理 int/string/null/其他
4. （选做）写 record struct `Vec3(double X, double Y, double Z)`，加一个方法 `Length()`

## 打卡清单
- [ ] 会用一行语法声明 record
- [ ] 理解 record 与 class 相等比较的区别
- [ ] 会用 with 表达式复制修改
- [ ] 会用 is 与 switch 做模式匹配
