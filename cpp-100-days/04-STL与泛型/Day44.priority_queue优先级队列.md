# Day 44 · priority_queue优先级队列

## 今日目标
- 掌握 `std::priority_queue`
- 理解堆的概念
- 学会比较函数的写法

## 学习内容

### 是什么

`std::priority_queue<T>` 每次 `top()` 取出的都是"最大"（或最小）元素，底层是堆：

```cpp
#include <queue>

std::priority_queue<int> pq;
pq.push(3);
pq.push(1);
pq.push(4);
pq.push(1);
pq.push(5);

std::cout << pq.top();   // 5（默认最大堆）
pq.pop();
std::cout << pq.top();   // 4
```

### 默认是最大堆

`priority_queue<int>` 默认用 `std::less<int>`，所以 top 是最大元素。

### 改成最小堆

```cpp
std::priority_queue<int, std::vector<int>, std::greater<int>> minPq;
minPq.push(3);
minPq.push(1);
minPq.push(4);
std::cout << minPq.top();   // 1
```

三个模板参数：

1. 元素类型；
2. 底层容器（默认 vector）；
3. 比较函数（默认 less）。

### 常用接口

- `push(x)`：插入，O(log n)；
- `top()`：看堆顶，O(1)；
- `pop()`：弹堆顶，O(log n)；
- `size()`、`empty()`。

### 自定义类型

让 `Student` 按分数进优先级队列：

```cpp
struct Student {
    std::string name;
    double score;
};

struct Cmp {
    bool operator()(const Student& a, const Student& b) const {
        return a.score < b.score;    // 分数高的在堆顶
    }
};

std::priority_queue<Student, std::vector<Student>, Cmp> pq;
```

### 典型应用

- 任务调度（优先级高的先执行）；
- Top K 问题；
- Dijkstra 最短路径。

## 核心重点
- priority_queue 每次取最大/最小
- 默认最大堆，要最小堆加 `greater<int>`
- 插入/弹出 O(log n)
- 自定义类型写比较函数对象
- Top K 问题常用

## 练习/任务
1. 用默认 priority_queue 对一组数排序输出
2. 用最小堆求一组数的 Top 3 小元素
3. 把 Student 按分数做成优先级队列
4. （选做）用 priority_queue 实现"合并 k 个有序链表"的思路（先不写代码）

## 今日刷题

- **LeetCode 215. 数组中的第 K 个最大元素**：大小为 k 的最小堆，遍历后堆顶就是第 k 大。
- **LeetCode 347. 前 K 个高频元素**：先统计频率，再用最小堆保留前 k。
- **LeetCode 23. 合并 K 个升序链表**：最小堆放每个链表当前节点，每次取最小接结果。

## 打卡清单
- [ ] 已会用默认最大堆
- [ ] 已会改成最小堆
- [ ] 已会写自定义比较
- [ ] 已完成 Top K 刷题
