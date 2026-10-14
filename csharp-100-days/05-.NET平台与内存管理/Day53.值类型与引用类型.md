# Day 53 · 值类型与引用类型

## 今日目标
- 彻底搞清 C# 两大类类型：值类型 vs 引用类型
- 理解为什么 `string` 是引用类型却像值类型
- 看清楚赋值、传参时到底发生了什么

## 学习内容

### 两大类

| 类别 | 代表 | 存哪 |
|---|---|---|
| 值类型 | int、double、bool、char、struct、enum | 栈（或内嵌在包含它的对象里） |
| 引用类型 | class、string、数组、List、object | 堆（变量存引用） |

### 赋值时的差别

```csharp
// 值类型：复制整个值
int a = 10;
int b = a;
b = 99;
Console.WriteLine(a);   // 10，不受影响

// 引用类型：复制引用，指向同一个对象
Student s1 = new() { Name = "A" };
Student s2 = s1;
s2.Name = "B";
Console.WriteLine(s1.Name);   // B！s1 和 s2 指向同一个对象
```

### 传参时的差别

```csharp
static void TryChangeInt(int x) { x = 999; }
static void TryChangeStudent(Student s) { s.Name = "被改了"; }

int n = 1;
TryChangeInt(n);
Console.WriteLine(n);        // 1

Student stu = new() { Name = "原" };
TryChangeStudent(stu);
Console.WriteLine(stu.Name);  // 被改了
```

值类型传副本；引用类型传引用副本，方法内改对象内容会反映到外面。

### string 特殊

`string` 是引用类型，但：

1. **不可变**：任何"修改"都返回新字符串；
2. `==` 比较内容而不是引用；

所以它"看起来像"值类型。

### null 是什么

引用类型变量可以是 `null`（不指向任何对象）；值类型默认不能为 null，要加 `?`：

```csharp
int? x = null;     // 可空值类型
string? s = null;  // 可空引用类型（C# 8+）
```

## 核心重点
- 值类型：int/struct/enum，赋值传副本
- 引用类型：class/string/数组，赋值传引用
- 引用类型传参，方法内改对象外面看得见
- string 不可变，`==` 比内容
- 可空性用 `?` 标注，C# 8+ 项目默认启用

## 练习/任务
1. 写两段代码分别验证 int 与 class 的赋值行为
2. 写方法修改传入的 Student 的 Name，观察外面是否变化
3. 验证 string 不可变：`string s="abc"; s.ToUpper();` 打印 s
4. （选做）写一个 struct 与一个 class，对比它们作为方法参数传递的行为

## 今日刷题
- **LeetCode 88 合并两个有序数组**（复习）：从后往前合并。今天重点理解数组是引用类型。
- **LeetCode 274 H 指数**：论文引用数排序，找最大 h 使至少 h 篇被引 ≥ h。思路：排序后二分或遍历。

## 打卡清单
- [ ] 能列出常见值类型与引用类型
- [ ] 理解赋值与传参的差别
- [ ] 理解 string 为什么像值类型
- [ ] 已完成至少一道今日刷题
