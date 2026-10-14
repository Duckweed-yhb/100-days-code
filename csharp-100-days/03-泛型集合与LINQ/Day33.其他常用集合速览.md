# Day 33 · 其他常用集合速览

## 今日目标
- 知道除了 List/Dictionary/HashSet 还有哪些集合
- 能根据场景选合适的集合
- 认识 `SortedDictionary`、`LinkedList`、`ReadOnlyCollection`

## 学习内容

### 集合家族一览

| 集合 | 特点 | 典型场景 |
|---|---|---|
| `List<T>` | 动态数组，按下标快 | 通用列表 |
| `Dictionary<K,V>` | 键值对，查找 O(1) | 索引查找 |
| `HashSet<T>` | 不重复，O(1) 判存在 | 去重、集合运算 |
| `Queue<T>` | FIFO | 排队、BFS |
| `Stack<T>` | LIFO | 括号、撤销 |
| `SortedDictionary<K,V>` | 按键有序 | 需要排序遍历键 |
| `SortedSet<T>` | 不重复且有序 | 范围查询 |
| `LinkedList<T>` | 双向链表 | 频繁中间插入删除 |
| `ObservableCollection<T>` | 变了会通知 | UI 绑定 |

### SortedSet 例子

```csharp
SortedSet<int> set = new() { 5, 1, 4, 2, 3 };
foreach (int x in set) Console.WriteLine(x);   // 1,2,3,4,5 自动排序

Console.WriteLine(set.Min);   // 1
Console.WriteLine(set.Max);   // 5
```

### LinkedList 简单认识

```csharp
LinkedList<int> ll = new();
LinkedListNode<int> node = ll.AddLast(10);
ll.AddAfter(node, 20);     // 在 node 后插 20
ll.AddFirst(0);
```

> 一般业务用 List 就够，LinkedList 仅在"频繁在中间插入删除"时才考虑。

### 数组与集合的互转

```csharp
int[] arr = { 1, 2, 3 };
List<int> list = arr.ToList();              // 数组 → List
int[] back = list.ToArray();                 // List → 数组
```

### 选集合决策树

1. 要按键查值？→ Dictionary
2. 要去重/快速判存在？→ HashSet
3. 要排队？→ Queue
4. 要撤销/嵌套？→ Stack
5. 要排序遍历？→ SortedSet/SortedDictionary
6. 其他绝大多数情况 → List\<T>

## 核心重点
- 别只会 List：不同场景选不同集合
- SortedSet 自动排序、不重复
- LinkedList 适合频繁中间增删
- 数组与集合用 ToList/ToArray 互转
- 选集合先想"我要怎么查、怎么遍历"

## 练习/任务
1. 用 SortedSet 存一组分数，遍历输出时自动有序
2. 用 LinkedList 在一个序列中间位置插入元素，体会与 List 的区别
3. 给定一组学生对象，分别用 List、Dictionary（按学号）、HashSet（去重）存，对比查找某个学生的写法
4. （选做）查 `ConcurrentBag<T>` 是干嘛的（后面并发阶段会遇到）

## 今日刷题
- **LeetCode 703 数据流第 K 大元素**：设计类 KthLargest，每次 add 返回当前第 K 大。思路：用 SortedSet（或容量为 K 的最小堆思路）保留最大的 K 个元素。
- **LeetCode 118 杨辉三角**：生成前 n 行。思路：每行首尾是 1，中间每个数 = 上一行相邻两数之和。用 List<List<int>> 存。

## 打卡清单
- [ ] 能根据场景选合适集合
- [ ] 会用 SortedSet 做有序去重
- [ ] 会在数组与 List 间互转
- [ ] 已完成至少一道今日刷题
