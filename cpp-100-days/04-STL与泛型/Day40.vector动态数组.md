# Day 40 · vector动态数组

## 今日目标
- 掌握 `std::vector` 的常用操作
- 理解它与原生数组的区别
- 学会在算法题里熟练使用 vector

## 学习内容

### vector 是什么

`std::vector<T>` 是 STL 提供的动态数组：连续内存、自动扩容、随机访问 O(1)。它几乎是 C++ 里最常用的容器。

```cpp
#include <vector>

std::vector<int> v;
v.push_back(10);
v.push_back(20);
v.push_back(30);
```

### 常用初始化

```cpp
std::vector<int> a;                  // 空
std::vector<int> b(5);               // 5 个 0
std::vector<int> c(5, -1);           // 5 个 -1
std::vector<int> d{1, 2, 3, 4};      // 列表初始化
std::vector<int> e = d;              // 拷贝
```

### 常用操作

| 操作 | 说明 |
|---|---|
| `v.push_back(x)` | 末尾追加 |
| `v.pop_back()` | 删末尾 |
| `v[i]` | 随机访问（不检查越界） |
| `v.at(i)` | 随机访问（越界抛异常） |
| `v.size()` | 元素个数 |
| `v.empty()` | 是否为空 |
| `v.clear()` | 清空 |
| `v.front()` / `v.back()` | 首尾元素 |
| `v.resize(n)` | 改变大小 |
| `v.resize(n, x)` | 新增元素填 x |

### 遍历

```cpp
for (int i = 0; i < v.size(); ++i) std::cout << v[i];
for (int x : v) std::cout << x;
```

### 二维 vector

```cpp
std::vector<std::vector<int>> mat(3, std::vector<int>(4, 0));
mat[1][2] = 5;
```

### 性能提示

- `push_back` 平均 O(1)，偶尔扩容 O(n)；
- 提前知道大小时用 `reserve(n)` 避免反复扩容；
- 随机访问 O(1)，中间插入/删除 O(n)。

### 与数组对比

| 对比 | 原生数组 | vector |
|---|---|---|
| 大小 | 编译期固定 | 动态 |
| 知道长度 | 不知道 | size() |
| 内存管理 | 手动 new/delete | 自动 |
| 越界检查 | 无 | at() 有 |

## 核心重点
- vector 是动态数组，连续内存
- 常用：push_back / pop_back / size / [] / front / back
- 范围 for `for (int x : v)` 最简洁
- 二维：`vector<vector<int>>`
- 提前 reserve 可提升性能

## 练习/任务
1. 用 vector 读入 n 个数，输出它们的和与平均值
2. 用 vector 实现斐波那契数列前 20 项
3. 写一个 3x3 二维 vector，打印它
4. （选做）读入 n 个数，用 vector 求最大值及其下标

## 今日刷题

- **LeetCode 88. 合并两个有序数组**：从后往前双指针，把 nums2 合并进 nums1。
- **LeetCode 26. 删除有序数组中的重复项**：慢指针记录下一个不重复位置，快指针扫描。
- **LeetCode 283. 移动零**：双指针，慢指针放非零，最后补零。

## 打卡清单
- [ ] 已熟练使用 vector 常用接口
- [ ] 已会写二维 vector
- [ ] 已能在刷题里用 vector
- [ ] 已完成至少一道数组刷题
