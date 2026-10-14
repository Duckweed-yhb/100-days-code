# Day 28 · List列表

## 今日目标
- 掌握 `List<T>` 动态数组的增删改查
- 理解它比数组好在哪
- 熟练使用常用方法

## 学习内容

### List<T> 是什么

数组长度固定，List<T> 长度可变，是 C# 里最常用的集合：

```csharp
List<int> nums = new List<int>();
nums.Add(10);
nums.Add(20);
nums.Add(30);

List<string> names = new() { "余泓彬", "张三", "李四" };
```

### 常用操作

```csharp
List<int> a = new() { 1, 2, 3 };

a.Add(4);                  // 末尾加
a.AddRange(new[] { 5, 6 });// 加一批
a.Insert(0, 0);            // 在下标 0 插
Console.WriteLine(a[2]);   // 按下标访问
a.Remove(3);               // 删除第一个等于 3 的
a.RemoveAt(0);             // 按下标删
Console.WriteLine(a.Count); // 元素个数
a.Contains(5);              // 是否包含
a.Sort();                   // 排序
a.Clear();                  // 清空
```

### 遍历

```csharp
List<string> fruits = new() { "苹果", "香蕉", "橙子" };

foreach (string f in fruits) Console.WriteLine(f);

for (int i = 0; i < fruits.Count; i++)
    Console.WriteLine($"{i}: {fruits[i]}");
```

### List vs 数组

| 对比 | 数组 `int[]` | List\<int\> |
|---|---|---|
| 长度 | 固定 | 动态 |
| 增删 | 麻烦 | Add/Remove 方便 |
| 性能 | 略快（连续内存） | 够用，日常首选 |
| 适用 | 长度已知且不变 | 绝大多数业务场景 |

### 小例子：成绩筛选

```csharp
List<int> scores = new() { 95, 55, 78, 40, 88, 60 };
List<int> passed = new List<int>();

foreach (int s in scores)
    if (s >= 60) passed.Add(s);

Console.WriteLine($"及格 {passed.Count} 人");
passed.ForEach(s => Console.WriteLine(s));   // Lambda，Day40 细讲
```

## 核心重点
- `List<T>` 是动态数组，长度可变
- Add/Insert/Remove/RemoveAt/Count/Contains 是高频 API
- 用 `foreach` 或 `for` 遍历
- 业务开发默认用 List，别再硬写数组
- 初始化器 `new() { ... }` 很方便

## 练习/任务
1. 用 List 存 10 个随机数，排序后输出
2. 写程序读用户输入若干单词，输入空串结束，按长度从长到短排序输出
3. 写方法 `RemoveDuplicates(List<int> a)` 去除重复元素，保留首次出现顺序
4. （选做）实现一个简单栈：内部 List，提供 Push/Pop/Count

## 今日刷题
- **LeetCode 283 移动零**：把数组里所有 0 移到末尾，保持非零元素相对顺序。思路：双指针，一个指写位置，一个扫；扫到非零就写到写指针位置，最后把写指针之后补 0。C# 用 List 或数组都行。
- **LeetCode 1 两数之和**：数组里找两个数加起来等于 target，返回下标。今天先暴力 O(n²) 两重循环；明天学 Dictionary 后再回头优化到 O(n)。

## 打卡清单
- [ ] 会用 List<T> 增删改查
- [ ] 会用 Sort/Contains/Count
- [ ] 理解 List 与数组的取舍
- [ ] 已完成至少一道今日刷题
