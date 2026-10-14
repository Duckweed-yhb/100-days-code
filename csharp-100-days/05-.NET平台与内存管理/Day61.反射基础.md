# Day 61 · 反射基础

## 今日目标
- 理解反射：运行时查看类型信息、调用成员
- 会用 `Type` 与 `PropertyInfo`
- 知道反射的典型用途与代价

## 学习内容

### 反射是什么

正常写代码时，你在编译期就知道要 new 哪个类、调哪个方法。反射让你在**运行时**才拿到类型信息、动态调用：

```csharp
Type t = typeof(Student);
Console.WriteLine(t.Name);            // Student
foreach (var p in t.GetProperties())
    Console.WriteLine($"属性：{p.Name} : {p.PropertyType.Name}");
```

### 动态创建对象、调方法

```csharp
Type t = typeof(Student);
object? obj = Activator.CreateInstance(t);   // 动态 new

// 动态设属性
t.GetProperty("Name")!.SetValue(obj, "余泓彬");

// 动态调方法
t.GetMethod("Introduce")!.Invoke(obj, null);
```

### 常见用途

- **序列化器**：JSON.NET、System.Text.Json 靠反射读对象属性；
- **依赖注入容器**：反射构造函数来 new 对象；
- **ORM**：EF Core 用反射把数据库行映射成对象；
- **插件系统**：加载 dll，扫描里面的类型。

### 反射的代价

- 比直接调用慢（要查元数据、做参数检查）；
- 失去编译期检查：拼错属性名编译不报错，运行时才炸；
- 代码难重构。

> 平时业务代码别滥用反射；框架里用是合理的。

### 反射特性：GetType vs typeof

```csharp
Student s = new();
Type t1 = s.GetType();    // 从实例拿
Type t2 = typeof(Student); // 从类型拿
```

## 核心重点
- 反射在运行时查看类型信息
- Type / PropertyInfo / MethodInfo 是核心
- Activator.CreateInstance 动态创建对象
- 序列化、DI、ORM 都靠反射
- 反射慢且丢编译期检查，业务代码别滥用

## 练习/任务
1. 写个方法打印任意对象的所有属性名与值（用反射）
2. 用反射动态创建一个 Student 并设 Name 属性
3. 对比直接 `s.Name="A"` 与反射 `SetValue` 的写法
4. （选做）查 `dynamic` 关键字与反射的关系

## 今日刷题
- **LeetCode 219 存在重复元素 II**：两个相同元素下标差 ≤ k。思路：Dictionary 记录每个数上次出现位置。
- **LeetCode 228 汇总区间**：连续区间摘要。思路：遍历，记录起点，断了就输出一段。

## 打卡清单
- [ ] 理解反射能做什么
- [ ] 会用 Type 拿属性信息
- [ ] 知道反射的代价
- [ ] 已完成至少一道今日刷题
