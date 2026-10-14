# Day 45 · set与multiset

## 今日目标
- 掌握 `std::set` 集合
- 了解 `std::multiset` 允许重复
- 理解底层红黑树

## 学习内容

### set 是什么

`std::set<T>` 是**有序、不重复**的集合，底层红黑树，插入/查找/删除 O(log n)：

```cpp
#include <set>

std::set<int> s;
s.insert(3);
s.insert(1);
s.insert(2);
s.insert(3);     // 重复，被忽略

for (int x : s) std::cout << x << ' ';   // 1 2 3（有序）
```

### 常用操作

| 操作 | 说明 |
|---|---|
| `s.insert(x)` | 插入 |
| `s.erase(x)` | 删除 |
| `s.count(x)` | 是否存在（0 或 1） |
| `s.find(x)` | 返回迭代器，找不到是 end() |
| `s.size()` / `s.empty()` / `s.clear()` | |
| `s.lower_bound(x)` | 第一个 >= x 的位置 |
| `s.upper_bound(x)` | 第一个 > x 的位置 |

### 判断存在

```cpp
if (s.count(42)) { /* 存在 */ }
```

因为 set 不重复，`count` 只会返回 0 或 1。

### multiset 允许重复

`std::multiset<T>` 允许重复元素：

```cpp
std::multiset<int> ms;
ms.insert(1);
ms.insert(1);
ms.insert(2);
// ms.size() == 3
```

注意：`ms.erase(1)` 会删掉**所有** 1；只想删一个要传迭代器：`ms.erase(ms.find(1))`。

### 选择

- 只要"去重 + 有序"：set；
- 允许重复：multiset；
- 只要去重不在乎顺序：`std::unordered_set`（Day46 讲 map 时一起提）。

## 核心重点
- set 有序不重复，O(log n)
- multiset 有序可重复
- count 判断存在
- erase(value) 在 multiset 里会删所有同值
- 底层红黑树

## 练习/任务
1. 用 set 给一组数去重并排序
2. 读入 n 个单词，用 set 统计不同单词数
3. 用 multiset 统计一组分数，去掉一个最高分和一个最低分
4. （选做）用 set 实现"两数之和"：遍历数组，查 `target - x` 在不在 set 里

## 今日刷题

- **LeetCode 1. 两数之和**：用哈希表存"值 -> 下标"，遍历一遍查 `target - x`。
- **LeetCode 217. 存在重复元素**：边遍历边插入 set，发现已存在就返回 true。
- **LeetCode 349. 两个数组的交集**：把一个数组塞 set，遍历另一个查存在。

## 打卡清单
- [ ] 已会用 set 去重
- [ ] 已会用 multiset
- [ ] 已理解 erase(value) 在 multiset 的坑
- [ ] 已完成 set 相关刷题
