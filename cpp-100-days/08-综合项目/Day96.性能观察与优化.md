# Day 96 · 性能观察与优化

## 今日目标
- 学会测量代码耗时
- 找热点
- 做小而正确的优化

## 学习内容

### 计时

```cpp
#include <chrono>

auto start = std::chrono::steady_clock::now();
// ... 要测的代码 ...
auto end = std::chrono::steady_clock::now();
auto ms = std::chrono::duration_cast<std::chrono::milliseconds>(end - start).count();
std::cout << "耗时：" << ms << " ms\n";
```

### 我们的项目性能瓶颈

对 10000 个任务做一次 list：

- 遍历 vector：O(n)，快；
- 每次 add 找下一个 id：O(1)；
- 删除按 id 查找：O(n)。

如果任务量大，删除可以改用 `unordered_map<int, Task>` 索引。但今天数据量小，先不优化。

### 优化原则

1. **先测量，再优化**：别凭感觉改；
2. **保持正确**：优化不能破坏功能；
3. **算法 > 微观优化**：O(n) 变 O(log n) 比循环里少两次赋值重要得多；
4. **别过度优化**：清晰优先。

### 小优化点

- `pass by const ref` 而非值：避免 string 拷贝；
- 预 reserve vector：`tasks_.reserve(1000);`；
- 用 emplace_back 代替 push_back。

### 不要做的事

- 为了快而写难懂的代码；
- 过早优化数据结构；
- 怀疑编译器。

## 核心重点
- std::chrono 测耗时
- 先测量再优化
- 算法改进 > 微观调优
- const ref / reserve / emplace 是好习惯
- 清晰正确优先

## 练习/任务
1. 加一个 benchmark：添加 10000 个任务并 list，测耗时
2. 给 tasks_ 加 reserve(1000)，看是否变快
3. 把所有传值参数改成 const ref
4. （选做）对比用 vector 查找 vs unordered_map 查找 id 的耗时

## 今日刷题

- **LeetCode 56. 合并区间**。
- **LeetCode 57. 插入区间**。
- **LeetCode 252. 会议室**。

## 打卡清单
- [ ] 已会用 chrono 计时
- [ ] 已测过项目耗时
- [ ] 已做 const ref 优化
- [ ] 已理解"先测量再优化"
