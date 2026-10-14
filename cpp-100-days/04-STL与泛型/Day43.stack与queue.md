# Day 43 · stack与queue

## 今日目标
- 掌握 `std::stack` 栈
- 掌握 `std::queue` 队列
- 理解它们是容器适配器

## 学习内容

### stack 栈

后进先出（LIFO）：

```cpp
#include <stack>

std::stack<int> st;
st.push(1);
st.push(2);
st.push(3);
// 栈顶是 3
std::cout << st.top();    // 3
st.pop();                 // 弹出 3
std::cout << st.top();    // 2
```

常用接口：`push / pop / top / empty / size`。

### queue 队列

先进先出（FIFO）：

```cpp
#include <queue>

std::queue<int> q;
q.push(1);
q.push(2);
q.push(3);
std::cout << q.front();   // 1
q.pop();
std::cout << q.front();   // 2
```

常用接口：`push / pop / front / back / empty / size`。

### 容器适配器

stack 和 queue 不是独立容器，而是**封装了一个底层容器**：

- stack 默认基于 `deque`；
- queue 默认基于 `deque`。

你也可以指定底层：`std::stack<int, std::vector<int>>`。

### 经典应用

- stack：括号匹配、表达式求值、DFS、单调栈；
- queue：BFS、消息队列、打印任务。

## 核心重点
- stack LIFO：push/pop/top
- queue FIFO：push/pop/front/back
- 它们是容器适配器，默认用 deque
- stack 用于 DFS，queue 用于 BFS

## 练习/任务
1. 用 stack 检查括号匹配：输入 `({[]})` 输出 true
2. 用 queue 模拟打印任务：入队 5 个任务，依次出队打印
3. 用两个 stack 实现一个 queue（Day39 题）
4. （选做）用 stack 把十进制数转二进制输出

## 今日刷题

- **LeetCode 225. 用队列实现栈**：两个队列，push 时塞到非空队列，pop 时把前 n-1 个搬到另一个队列。
- **LeetCode 232. 用栈实现队列**：两个栈，push 进栈 A，pop 时若栈 B 空就把 A 全倒到 B。
- **LeetCode 496. 下一个更大元素 I**：单调栈模板题。

## 打卡清单
- [ ] 已会用 stack 与 queue
- [ ] 已理解它们是容器适配器
- [ ] 已完成括号匹配
- [ ] 已完成至少一道栈/队列刷题
