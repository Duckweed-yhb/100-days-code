# Day 57 · 各版本标准特性速览

## 今日目标
- 梳理 C++11/14/17 各版本常用特性
- 知道哪些已经是日常工具
- 明确不使用 C++20/23 特性

## 学习内容

### C++11：现代 C++ 的起点

C++11 是最重要的版本，几乎重写了语言：

- 类型推导 `auto` / `decltype`；
- 范围 for；
- lambda 表达式；
- 智能指针（unique_ptr/shared_ptr/weak_ptr）；
- 移动语义与右值引用；
- 列表初始化 `{...}`；
- `nullptr`；
- `=default` / `=delete`；
- 可变参数模板；
- `enum class`；
- 多线程库 thread/mutex/future。

### C++14：小而美

- `make_unique`；
- 函数返回值 auto；
- 泛型 lambda（参数用 auto）；
- 数字分隔符 `1'000'000`；
- 变量模板。

### C++17：成熟

- 结构化绑定 `auto [a, b] = pair;`；
- `if constexpr`；
- 类模板参数推导（`pair p{1, 2.0};`）；
- `std::optional` / `std::variant`；
- 嵌套命名空间 `namespace a::b {}`；
- `std::string_view`。

### 不要用的 C++20/23 特性

本路线只到 C++17。以下特性**不要用**：

- concepts（概念）；
- co_await / coroutines（协程）；
- ranges（范围库）；
- modules（模块）；
- three-way compare `<=>`；
- std::format；
- std::span（C++20）。

编译时统一加 `-std=c++17`。

### 日常必备清单

每天写代码都用得到：auto、范围 for、lambda、智能指针、列表初始化、`nullptr`、结构化绑定。其他特性知道在哪查即可。

## 核心重点
- C++11 是现代 C++ 分水岭
- C++14/17 在日常工程已普及
- 本路线上限 C++17
- 不碰 C++20/23 的 concepts/coroutines/ranges/modules
- 编译加 -std=c++17

## 练习/任务
1. 把今天列的特性清单抄一遍
2. 在你之前的代码里找出 5 处可以用现代 C++ 改写的地方
3. 确认编译命令带 -std=c++17
4. （选做）查资料了解 std::optional 是做什么的

## 今日刷题

- **LeetCode 121. 买卖股票的最佳时机**：一次遍历维护最低价。
- **LeetCode 53. 最大子序和**：DP。
- **LeetCode 152. 乘积最大子数组**：维护最大最小。

## 打卡清单
- [ ] 已梳理 C++11/14/17 特性
- [ ] 已知道不碰 C++20/23
- [ ] 已确认编译用 -std=c++17
- [ ] 已完成至少一道 DP 题
