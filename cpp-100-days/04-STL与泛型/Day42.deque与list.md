# Day 42 · deque与list

## 今日目标
- 了解 `std::deque` 双端队列
- 掌握 `std::list` 双向链表
- 知道什么时候该用它们而不是 vector

## 学习内容

### deque 双端队列

`std::deque<T>` 支持头尾 O(1) 插入删除：

```cpp
#include <deque>

std::deque<int> d;
d.push_back(1);
d.push_front(0);
d.push_back(2);
// d: [0, 1, 2]
d.pop_front();   // 删 0
d.pop_back();    // 删 2
```

特点：

- 头尾增删 O(1)；
- 中间增删 O(n)；
- 随机访问 O(1)（比 vector 稍慢）；
- 内存不是严格连续，但对外像连续。

### list 双向链表

`std::list<T>` 是双向链表：

```cpp
#include <list>

std::list<int> l = {1, 2, 3};
l.push_back(4);
l.push_front(0);
// 已知迭代器位置时，插入删除 O(1)
```

特点：

- 任意位置插入删除 O(1)（已知位置）；
- **不支持随机访问**：`l[0]` 编译错误；
- 遍历只能 `++iter` 一个个走；
- 每个节点有前后指针，额外内存开销大。

### 怎么选容器

| 场景 | 选择 |
|---|---|
| 末尾增删、随机访问 | vector |
| 头尾都要增删 | deque |
| 已知位置频繁增删 | list |
| 其他 | 默认 vector |

工程实践里，list 用得很少——vector 的缓存局部性通常比 list 的"O(1) 删除"更值钱。

### list 的迭代器

```cpp
std::list<int> l = {1, 2, 3, 4};
for (auto it = l.begin(); it != l.end(); ++it) {
    std::cout << *it << ' ';
}
```

list 迭代器不支持 `+n`（因为不是连续内存），只能 `++`。

## 核心重点
- deque 头尾 O(1)，随机访问 O(1)
- list 是双向链表，任意位置 O(1) 删除，但不支持随机访问
- 默认用 vector，除非明确需要头尾插入
- list 用得少，因为缓存不友好

## 练习/任务
1. 用 deque 实现一个"双端队列"：从队尾入队，从队首出队
2. 用 list 存一组整数，遍历并打印
3. 在 list 中间某个位置插入一个元素（用迭代器）
4. （选做）对比 vector 和 list 在中间插入 1 万元素的耗时（粗测即可）

## 今日刷题

- **LeetCode 239. 滑动窗口最大值**：用 deque 维护一个单调递减队列，队首是当前窗口最大值。
- **LeetCode 150. 逆波兰表达式求值**：遇到数字入栈，遇到运算符弹出两个计算后入栈。
- **LeetCode 71. 简化路径**：用栈处理 `.` `..`，最后拼接。

## 打卡清单
- [ ] 已会用 deque 头尾操作
- [ ] 已会用 list 基本遍历
- [ ] 已知道默认选 vector
- [ ] 已完成至少一道栈/队列刷题
