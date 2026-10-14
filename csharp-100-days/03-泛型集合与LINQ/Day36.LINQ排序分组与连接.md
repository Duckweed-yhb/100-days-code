# Day 36 · LINQ排序分组与连接

## 今日目标
- 掌握 OrderBy/ThenBy 多级排序
- 会用 GroupBy 分组
- 了解 Join 连接两个集合

## 学习内容

### 多级排序

```csharp
List<Student> students = GetStudents();

var sorted = students
    .OrderBy(s => s.Score)          // 先按成绩升
    .ThenBy(s => s.Name)            // 成绩相同按名字
    .ToList();

// 降序
var desc = students.OrderByDescending(s => s.Score);
```

### GroupBy 分组

按某个键把元素分到一组：

```csharp
var groups = students.GroupBy(s => s.Score >= 60 ? "及格" : "不及格");

foreach (var g in groups)
{
    Console.WriteLine($"=== {g.Key} ({g.Count()} 人) ===");
    foreach (var s in g)
        Console.WriteLine($"  {s.Name}");
}
```

也可以按 Category、按姓分组：

```csharp
var byCategory = products.GroupBy(p => p.Category);
foreach (var g in byCategory)
{
    Console.WriteLine($"{g.Key}: 平均价 {g.Average(p => p.Price):F2}");
}
```

### SelectMany：拍平嵌套

```csharp
List<List<int>> nested = new() { new(){1,2}, new(){3,4} };
IEnumerable<int> flat = nested.SelectMany(x => x);
// 1,2,3,4
```

### Join：像 SQL 一样连接

```csharp
List<Student> students = GetStudents();
List<ClassInfo> classes = GetClasses();

var query = students.Join(classes,
    s => s.ClassId,           // 学生外键
    c => c.Id,                // 班级主键
    (s, c) => new { s.Name, ClassName = c.Name });
```

### ToLookup（了解）

`GroupBy` 是延迟执行；`ToLookup` 立刻算出只读查找表，适合多次按键查：

```csharp
var lookup = students.ToLookup(s => s.ClassId);
foreach (var s in lookup[3]) Console.WriteLine(s.Name);   // 3 班所有学生
```

## 核心重点
- OrderBy + ThenBy 多级排序
- GroupBy 按键分组，每组是 IGrouping（Key + 元素集合）
- SelectMany 把嵌套集合拍平
- Join 连接两个集合（按外键）
- ToLookup 适合多次按键查

## 练习/任务
1. 一组学生按"班级"分组，每组打印姓名与平均分
2. 一组商品按"类别"分组，求每类总价
3. 用 OrderBy+ThenBy 先按年龄、同年龄按姓名排序
4. （选做）用 Join 把"订单"与"客户"连起来，打印订单号与客户名

## 今日刷题
- **LeetCode 49 字母异位词分组**：把字母组成相同的词分到一组。思路：每个词排序后的字符串当 GroupBy 的键，分组后返回各组。C#：`strs.GroupBy(s => string.Concat(s.OrderBy(c=>c)))`。
- **LeetCode 56 合并区间**：若干区间 [start,end]，合并重叠的。思路：按 start 排序，遍历若当前区间起点 ≤ 上一个终点则合并，否则开新区间。

## 打卡清单
- [ ] 会用 OrderBy/ThenBy 多级排序
- [ ] 会用 GroupBy 分组并聚合
- [ ] 会用 Join 连接两个集合
- [ ] 已完成至少一道今日刷题
