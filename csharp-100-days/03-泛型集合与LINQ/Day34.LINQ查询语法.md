# Day 34 · LINQ查询语法

## 今日目标
- 理解 LINQ 是什么、解决什么问题
- 掌握 from/where/select 查询表达式
- 体会"用 SQL 思维处理集合"

## 学习内容

### LINQ 是什么

LINQ（Language Integrated Query，语言集成查询）让你用统一的语法查集合、数据库、XML。前面我们手写循环筛选，LINQ 把它变声明式：

```csharp
List<int> nums = new() { 3, 1, 4, 1, 5, 9, 2, 6 };

// 手写循环
List<int> even = new();
foreach (int n in nums)
    if (n % 2 == 0) even.Add(n);

// LINQ 查询语法
IEnumerable<int> evenLinq = from n in nums
                            where n % 2 == 0
                            select n;
```

### 查询表达式结构

```csharp
var result = from 元素 in 集合
             where 条件
             orderby 排序字段
             select 投影;
```

例子：

```csharp
List<Student> students = new()
{
    new() { Name = "余泓彬", Age = 20, Score = 90 },
    new() { Name = "张三",   Age = 22, Score = 55 },
    new() { Name = "李四",   Age = 19, Score = 78 },
};

var query = from s in students
            where s.Score >= 60
            orderby s.Score descending
            select new { s.Name, s.Score };

foreach (var x in query)
    Console.WriteLine($"{x.Name}: {x.Score}");
```

### 延迟执行

LINQ 查询建好后**不会立刻执行**，直到你遍历它（`foreach`、`ToList`）。这意味着你可以先拼查询，最后再执行。

```csharp
var q = from s in students where s.Score >= 60 select s;   // 还没执行
students.Add(new() { Name = "王五", Age = 21, Score = 95 });
foreach (var s in q) Console.WriteLine(s.Name);   // 王五也会出现
```

### 必须 using

LINQ 方法在 `System.Linq` 命名空间，现代项目默认隐式 using 已包含。

## 核心重点
- LINQ 用统一语法查集合/数据库
- 查询表达式：from ... where ... orderby ... select ...
- select 可以投影成匿名类型 `new { ... }`
- 查询延迟执行，遍历时才跑
- 与 SQL 思维类似，但在内存里跑

## 练习/任务
1. 用 LINQ 查询语法从一组数字里挑出偶数并排序
2. 从学生列表里查出所有及格学生，按成绩降序
3. 把上面结果投影成"姓名-成绩"匿名类型，遍历打印
4. （选做）试试在查询建好后往集合里加元素，看遍历结果是否变化，理解延迟执行

## 今日刷题
- **LeetCode 349 两个数组的交集（LINQ 版）**：`nums1.Intersect(nums2).ToList()` 一行搞定。先体验 LINQ 的威力，再回头看底层。
- **LeetCode 350 两个数组的交集 II**：允许重复。思路：GroupBy 分组计数，按较少的那个取。C# LINQ：`nums1.GroupBy(x=>x).Join(nums2.GroupBy(y=>y), g=>g.Key, g=>g.Key, (a,b)=>a.Take(Math.Min(a.Count(),b.Count())))` 再 SelectMany。

## 打卡清单
- [ ] 会写 from/where/select 查询
- [ ] 会用 orderby 排序
- [ ] 会投影成匿名类型
- [ ] 已完成至少一道今日刷题
