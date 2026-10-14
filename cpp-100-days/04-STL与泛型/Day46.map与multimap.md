# Day 46 · map与multimap

## 今日目标
- 掌握 `std::map` 键值对
- 了解 `std::unordered_map`
- 学会在算法题里用 map 做计数

## 学习内容

### map 是什么

`std::map<K, V>` 是有序键值对，按键排序，底层红黑树：

```cpp
#include <map>

std::map<std::string, int> scores;
scores["张三"] = 90;
scores["李四"] = 85;
scores["王五"] = 95;

for (const auto& [name, score] : scores) {   // C++17 结构化绑定
    std::cout << name << ": " << score << '\n';
}
```

### 常用操作

| 操作 | 说明 |
|---|---|
| `m[key] = value` | 插入/修改 |
| `m.at(key)` | 访问（不存在抛异常） |
| `m.count(key)` | 键是否存在 |
| `m.find(key)` | 返回迭代器 |
| `m.erase(key)` | 删除 |
| `m.size()` / `m.empty()` | |

### [] 的坑

`m[key]` 如果 key 不存在，会**自动插入一个默认值**（int 就是 0）：

```cpp
std::map<std::string, int> m;
std::cout << m["abc"];    // 输出 0，但 m 里已经多了一项
```

只想查不插，用 `m.count(key)` 或 `m.find(key)`。

### unordered_map

`std::unordered_map<K, V>` 底层哈希表，平均 O(1)：

```cpp
#include <unordered_map>

std::unordered_map<std::string, int> m;
m["hello"] = 1;
```

区别：

| 容器 | 顺序 | 查找 |
|---|---|---|
| map | 按键有序 | O(log n) |
| unordered_map | 无序 | 平均 O(1) |

刷题时优先用 unordered_map，更快。

### 计数典型用法

```cpp
std::unordered_map<std::string, int> freq;
for (const auto& word : words) {
    freq[word]++;        // 不存在时自动变 0，再 ++
}
```

### multimap

`std::multimap<K, V>` 允许同键多值，没有 `[]` 运算符（因为不知道选哪个）。用得少，了解即可。

## 核心重点
- map 按键有序，O(log n)；unordered_map 哈希，O(1)
- `m[key]` 不存在会自动插入默认值
- 计数用 `freq[word]++` 最方便
- 刷题优先 unordered_map
- multimap 同键多值，用得少

## 练习/任务
1. 统计一段英文中每个单词出现的次数，输出前 5 高频词
2. 用 map 实现"字典"：输入单词查释义
3. 用 unordered_map 重做两数之和
4. （选做）给定 n 个学生的姓名和成绩，按成绩排序输出

## 今日刷题

- **LeetCode 1. 两数之和**：unordered_map 存值->下标。
- **LeetCode 242. 有效的字母异位词**：用数组（26 字母）计数对比。
- **LeetCode 383. 赎金信**：统计 magazine 字符频率，看能不能凑出 ransomNote。

## 打卡清单
- [ ] 已会用 map 做键值存储
- [ ] 已知道 [] 的自动插入坑
- [ ] 已会用 unordered_map 计数
- [ ] 已完成至少一道哈希题
