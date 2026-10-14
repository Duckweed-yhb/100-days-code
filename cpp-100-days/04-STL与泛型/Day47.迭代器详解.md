# Day 47 · 迭代器详解

## 今日目标
- 理解迭代器的概念与分类
- 掌握 begin/end、迭代器失效问题
- 能用迭代器遍历各种容器

## 学习内容

### 迭代器是什么

迭代器（iterator）是一种"指向容器中元素"的对象，行为像指针：

```cpp
std::vector<int> v{1, 2, 3};
for (auto it = v.begin(); it != v.end(); ++it) {
    std::cout << *it << ' ';
}
```

- `v.begin()` 指向第一个元素；
- `v.end()` 指向"最后一个元素的下一个位置"；
- `*it` 取元素；
- `++it` 移到下一个。

### 五种迭代器

| 类别 | 支持操作 | 容器 |
|---|---|---|
| 输入/输出迭代器 | 只读/只写单次 | |
| 前向迭代器 | ++ | forward_list |
| 双向迭代器 | ++ -- | list, set, map |
| 随机访问迭代器 | ++ -- + - [] | vector, deque, string |

vector 的迭代器能 `it + 5`，list 的不行。

### const 迭代器

只读用 `cbegin()` / `cend()`：

```cpp
for (auto it = v.cbegin(); it != v.cend(); ++it) {
    // *it = 10;   // 错：const 迭代器不能改
}
```

### 反向迭代器

`rbegin()` / `rend()` 从后往前：

```cpp
for (auto it = v.rbegin(); it != v.rend(); ++it) {
    std::cout << *it;     // 倒序输出
}
```

### 迭代器失效

vector 中插入/删除可能导致迭代器失效：

```cpp
std::vector<int> v{1, 2, 3, 4};
for (auto it = v.begin(); it != v.end(); ) {
    if (*it % 2 == 0) it = v.erase(it);   // erase 返回下一个有效迭代器
    else ++it;
}
```

- `erase(it)` 返回被删元素的下一个位置；
- `push_back` 可能导致扩容，所有旧迭代器失效。

### 范围 for 的本质

`for (int x : v)` 底层就是 `begin()/end()` 迭代器。

## 核心重点
- 迭代器像泛型指针，统一遍历接口
- begin()/end()，*it 取值，++it 前进
- vector 随机访问，list/map 只能双向
- erase 返回下一个有效迭代器
- 插入/删除后旧迭代器可能失效

## 练习/任务
1. 用迭代器遍历 vector 并打印
2. 用反向迭代器倒序打印
3. 用迭代器删除 vector 中所有偶数（用 erase 返回值）
4. （选做）在 set 和 map 上用迭代器遍历

## 今日刷题

- **LeetCode 287. 寻找重复数**：快慢指针（Floyd 环）。
- **LeetCode 442. 数组中重复的数据**：把数组当哈希，用正负号标记访问过。
- **LeetCode 448. 找到所有数组中消失的数字**：把出现过的数对应位置标负，剩下正的就是消失的。

## 打卡清单
- [ ] 已会用迭代器遍历 vector
- [ ] 已会用反向迭代器
- [ ] 已理解迭代器失效
- [ ] 已完成至少一道数组刷题
