# Day 58 · 范围for与emplace

## 今日目标
- 熟练使用范围 for 循环
- 理解 emplace_back 与 push_back 的区别
- 了解 C++11/14/17 其他常用小特性

## 学习内容

### 范围 for 回顾

```cpp
std::vector<int> v{1, 2, 3};
for (int x : v) std::cout << x;          // 拷贝
for (int& x : v) x *= 2;                 // 改
for (const auto& x : v) std::cout << x;  // 只读，推荐
```

### emplace_back

`push_back` 要先构造对象再移动/拷贝进去；`emplace_back` 直接在容器内存里就地构造：

```cpp
std::vector<std::pair<int, std::string>> v;

v.push_back(std::make_pair(1, "a"));        // 先构造 pair，再移动
v.emplace_back(2, "b");                     // 直接在 vector 里构造 pair
```

对于 string、pair 这类有构造函数的类型，emplace_back 省一次移动。

### emplace 也用于 set/map

```cpp
std::map<std::string, int> m;
m.emplace("hello", 42);     // 直接构造 pair
```

### C++11 其他小特性回顾

- 列表初始化 `T x{...}`；
- nullptr；
- lambda；
- 智能指针；
- 移动语义；
- 范围 for；
- auto；
- =default / =delete。

### C++14 亮点

- `make_unique`；
- 函数返回 auto；
- 泛型 lambda：`[](auto a, auto b){ return a + b; }`；
- 数字分隔符：`1'000'000`。

### C++17 亮点

- 结构化绑定：`for (const auto& [k, v] : m)`；
- `if constexpr`（条件编译）；
- 类模板参数推导：`std::pair p{1, 2.0};` 不用写 `<int,double>`；
- `std::optional`、`std::variant`。

> 注意：今天列的 C++20 相关特性（concepts、co_await、ranges、modules）不要用。

## 核心重点
- 范围 for 用 `const auto&` 最稳
- emplace_back 就地构造，比 push_back 省一次移动
- C++11 是现代 C++ 起点
- C++14/17 在日常工作中已普及
- 不使用 C++20/23 特性

## 练习/任务
1. 把代码里所有 push_back 能换 emplace_back 的都换一下
2. 用结构化绑定遍历 map
3. 用数字分隔符写一个大数字字面量
4. （选做）写一个泛型 lambda 实现任意类型加法

## 今日刷题

- **LeetCode 121. 买卖股票的最佳时机**：一次遍历，维护最低价。
- **LeetCode 53. 最大子序和**：DP，`dp[i] = max(nums[i], dp[i-1]+nums[i])`。
- **LeetCode 152. 乘积最大子数组**：维护最大最小两个 DP 状态。

## 打卡清单
- [ ] 已会用 emplace_back
- [ ] 已会用结构化绑定
- [ ] 已知道 C++11/14/17 主要特性
- [ ] 已完成至少一道 DP 题
