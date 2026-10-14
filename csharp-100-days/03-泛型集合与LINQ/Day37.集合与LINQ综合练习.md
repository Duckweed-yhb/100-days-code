# Day 37 · 集合与LINQ综合练习

## 今日目标
- 综合运用 List/Dictionary/HashSet/Queue/Stack
- 用 LINQ 解决一个真实小问题
- 复盘整个阶段

## 学习内容

### 本阶段回顾

| 天 | 主题 | 关键 API |
|---|---|---|
| 26-27 | 泛型 | `class Box<T>`、`where T : ...` |
| 28 | List | Add/Insert/Remove/Sort |
| 29 | Dictionary | `dict[k]=v`、TryGetValue |
| 30 | HashSet | UnionWith/IntersectWith |
| 31 | Queue | Enqueue/Dequeue |
| 32 | Stack | Push/Pop |
| 33 | 其他集合 | SortedSet/LinkedList |
| 34-36 | LINQ | Where/Select/OrderBy/GroupBy/Join |

### 示范：学生成绩分析器

```csharp
List<Student> students = new()
{
    new() { Name="余泓彬", Age=20, ClassId=1, Score=95 },
    new() { Name="张三",   Age=22, ClassId=2, Score=55 },
    new() { Name="李四",   Age=19, ClassId=1, Score=78 },
    new() { Name="王五",   Age=21, ClassId=2, Score=88 },
    new() { Name="赵六",   Age=20, ClassId=1, Score=40 },
};

// 1. 每班平均分
Console.WriteLine("=== 每班平均分 ===");
foreach (var g in students.GroupBy(s => s.ClassId))
{
    Console.WriteLine($"班 {g.Key}: {g.Average(s => s.Score):F2}");
}

// 2. 及格名单按成绩降序
Console.WriteLine("\n=== 及格名单 ===");
var passed = students.Where(s => s.Score >= 60)
                     .OrderByDescending(s => s.Score)
                     .Select(s => $"{s.Name}({s.Score})");
Console.WriteLine(string.Join(", ", passed));

// 3. 最高/最低/总平均
Console.WriteLine($"\n最高 {students.Max(s=>s.Score)}，最低 {students.Min(s=>s.Score)}，平均 {students.Average(s=>s.Score):F2}");
```

### 什么时候别用 LINQ

- 极致性能热点：LINQ 有少量委托开销；
- 需要提前 break 的简单循环：直接写 foreach 更直观；
- 嵌套很深的复杂查询：拆成多步命名变量更易读。

## 核心重点
- 集合选对一半，代码省一半
- LINQ 让数据处理变声明式，别再手写一堆循环
- GroupBy 是分析数据的神器
- 但别为了炫技把简单问题复杂化
- 这阶段结束，你已能用 C# 处理大多数数据任务

## 练习/任务
1. 给定一组订单（订单号、客户名、金额、日期），用 LINQ 求：每个客户总消费、客单价最高的客户
2. 用 HashSet + LINQ 找出两个列表里的对称差（只在其中一个出现的元素）
3. 把一段英文按空格分词，用 LINQ 做词频统计，按次数降序输出前 5
4. （选做）用 Queue 模拟一个简单任务队列：任务有 ID 与耗时，按入队顺序处理

## 今日刷题
- **LeetCode 15 三数之和**：数组里找所有不重复三元组 a+b+c=0。思路：排序后固定一个 a，再双指针找 b、c；用 HashSet 或排序去重。C# 注意去重逻辑。
- **LeetCode 560 和为 K 的子数组**：连续子数组和为 K 的个数。思路：前缀和 + Dictionary，preSum[i] - preSum[j] = k 即 preSum[j] = preSum[i] - k，一边扫一边记前缀和出现次数。

## 打卡清单
- [ ] 已完成学生成绩分析器
- [ ] 会用 LINQ 做分组聚合
- [ ] 知道什么时候该用 LINQ、什么时候别用
- [ ] 已完成至少一道今日刷题
