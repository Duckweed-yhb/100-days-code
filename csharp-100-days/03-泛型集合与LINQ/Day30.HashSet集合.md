# Day 30 · HashSet集合

## 今日目标
- 掌握 `HashSet<T>` 去重与集合运算
- 理解它与 List、Dictionary 的区别
- 学会用它做"存在性判断"

## 学习内容

### HashSet 是什么

HashSet 是**不重复、无序**的集合，内部基于哈希表：

```csharp
HashSet<int> set = new() { 1, 2, 3, 3, 2, 1 };
Console.WriteLine(set.Count);     // 3，自动去重
foreach (int x in set) Console.WriteLine(x);
```

### 常用操作

```csharp
HashSet<string> names = new() { "Alice", "Bob", "Cara" };
names.Add("David");          // 加
names.Add("Alice");          // 已存在，加不进去，不报错
names.Remove("Bob");
Console.WriteLine(names.Contains("Alice"));   // true，O(1)
```

### 集合运算

```csharp
HashSet<int> a = new() { 1, 2, 3, 4 };
HashSet<int> b = new() { 3, 4, 5, 6 };

a.UnionWith(b);        // 并集：a 变成 {1,2,3,4,5,6}
a.IntersectWith(b);    // 交集：{3,4}
a.ExceptWith(b);       // 差集：a 去掉 b 里有的
```

### HashSet vs List vs Dictionary

| 集合 | 特点 | 典型用途 |
|---|---|---|
| List\<T> | 有序、可重复、按下标访问 | 顺序列表 |
| HashSet\<T> | 不重复、无序、快速查存在 | 去重、集合运算 |
| Dictionary\<K,V> | 键值对、键唯一 | 按键查值 |

### 例子：找共同好友

```csharp
HashSet<string> aliceFriends = new() { "Bob", "Cara", "David" };
HashSet<string> bobFriends = new() { "Cara", "Eve", "Frank" };

aliceFriends.IntersectWith(bobFriends);
Console.WriteLine("共同好友：");
foreach (string f in aliceFriends) Console.WriteLine(f);   // Cara
```

## 核心重点
- HashSet 自动去重，Contains 是 O(1)
- Add 重复元素不会报错，只是加不进去
- UnionWith/IntersectWith/ExceptWith 做并交差
- 要去重或快速判存在时选 HashSet
- 无序，别依赖遍历顺序

## 练习/任务
1. 读一组数字，用 HashSet 去重后输出
2. 两个班级学生名单，用集合运算找出：两班都选的课、只在 A 班选的课
3. 写方法 `bool HasDuplicate(List<int> a)`：用 HashSet 判断是否有重复
4. （选做）判断两个字符串是否字母组成相同（异位词），用 HashSet 做粗筛

## 今日刷题
- **LeetCode 349 两个数组的交集**：返回两个数组公共元素（去重）。思路：把第一个数组塞 HashSet，遍历第二个，遇到在集合里的就加入结果，再去重。
- **LeetCode 202 快乐数**：反复把各位平方和相加，最终到 1 就是快乐数；否则会循环。思路：用 HashSet 记录出现过的数，重复出现说明死循环。

## 打卡清单
- [ ] 会用 HashSet 去重
- [ ] 会做并集交集差集
- [ ] 理解 HashSet 与 List 的取舍
- [ ] 已完成至少一道今日刷题
