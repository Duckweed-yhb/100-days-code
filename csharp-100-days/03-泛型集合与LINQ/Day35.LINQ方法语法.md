# Day 35 · LINQ方法语法

## 今日目标
- 掌握 LINQ 方法语法（点号链）
- 熟练 Where/Select/First/Any/Count 等高频运算符
- 理解方法语法与查询语法的等价性

## 学习内容

### 方法语法更常用

实际项目里方法语法比查询语法更常见，因为可以链式拼接：

```csharp
List<int> nums = new() { 3, 1, 4, 1, 5, 9, 2, 6 };

var even = nums.Where(n => n % 2 == 0)
               .OrderBy(n => n)
               .ToList();
```

读作：从 nums 里"筛偶数 → 排序 → 转成 List"。

### 高频运算符

```csharp
List<int> a = new() { 3, 1, 4, 1, 5, 9, 2, 6 };

a.Where(x => x > 3);            // 筛选
a.Select(x => x * 2);           // 投影：每个元素变样
a.First();                      // 第一个；空集合抛异常
a.First(x => x > 5);            // 第一个满足条件的
a.FirstOrDefault();             // 空集合返回默认值（0/null）
a.Any();                        // 是否有元素
a.Any(x => x > 100);            // 是否存在满足条件的
a.All(x => x > 0);              // 是否全部满足
a.Count();                      // 个数
a.Count(x => x > 3);            // 满足条件的个数
a.Sum();                        // 求和
a.Max(); a.Min(); a.Average();  // 最大最小平均
a.Distinct();                   // 去重
```

### Select 投影

```csharp
List<Student> students = GetStudents();

// 只要名字
IEnumerable<string> names = students.Select(s => s.Name);

// 投影成匿名类型
var info = students.Select(s => new { s.Name, Passed = s.Score >= 60 });
```

### 链式调用

```csharp
var result = students
    .Where(s => s.Score >= 60)
    .OrderByDescending(s => s.Score)
    .Select(s => s.Name)
    .ToList();
```

读链：先筛、再排、再投影、最后落地成 List。

### 何时 ToList / ToArray

LINQ 默认延迟执行。要立刻算出来存内存，调 `ToList()`；要多次遍历避免重复查询，也 `ToList()`。

## 核心重点
- 方法语法 `.Where().Select().OrderBy()` 链式调用
- Where 筛、Select 投影、First/FirstOrDefault 取一个
- Any/All/Count 做布尔判断与计数
- Sum/Max/Min/Average 做聚合
- ToList 立刻执行并落地

## 练习/任务
1. 用方法语法把一组数字平方后排序
2. 从学生列表里：筛选及格、按分数降序、只取姓名，最后 ToList
3. 用 LINQ 一句话判断一个字符串数组里是否所有元素长度都 > 3
4. （选做）用 LINQ 求一组数的中位数（提示：先排序再取下中）

## 今日刷题
- **LeetCode 9 回文数**：判断整数是否回文。纯算法题，但可以用 LINQ：`x.ToString().ToCharArray()` 后用 `SequenceEqual` 与反转版比较。
- **LeetCode 14 最长公共前缀**：字符串数组找最长公共前缀。思路：以第一个串为基准逐字符比；C# 可结合 LINQ 做分组。

## 打卡清单
- [ ] 会写 Where/Select/OrderBy 链
- [ ] 会用 FirstOrDefault 安全取值
- [ ] 会用 Any/All/Count
- [ ] 已完成至少一道今日刷题
