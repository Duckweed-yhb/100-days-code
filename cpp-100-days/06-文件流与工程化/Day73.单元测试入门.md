# Day 73 · 单元测试入门

## 今日目标
- 理解为什么要写测试
- 学会用最简单的 assert 写测试
- 了解 Google Test 的基本结构

## 学习内容

### 为什么要单元测试

- 改代码不害怕：有测试兜底；
- 文档：测试用例就是用法示例；
- 重构信心：测试过了 = 没改坏。

### 最简单的测试：assert

```cpp
#include <cassert>
#include <cmath>

int add(int a, int b) { return a + b; }

int main() {
    assert(add(1, 2) == 3);
    assert(add(-1, 1) == 0);
    assert(add(0, 0) == 0);
    std::cout << "所有测试通过\n";
}
```

`assert(条件)` 失败就终止程序并打印位置。`#define NDEBUG` 后 assert 失效。

### 测试组织

```cpp
void test_add() {
    assert(add(1, 2) == 3);
    assert(add(-1, 1) == 0);
}

void test_max() {
    assert(max(1, 2) == 2);
}

int main() {
    test_add();
    test_max();
    std::cout << "OK\n";
}
```

### Google Test 简介

工业界常用 Google Test（gtest）：

```cpp
#include <gtest/gtest.h>

TEST(AddTest, Positive) {
    EXPECT_EQ(add(1, 2), 3);
}
TEST(AddTest, Negative) {
    EXPECT_EQ(add(-1, -2), -3);
}
```

今天先知道有这个东西，不用装。

### 测试什么

- 正常情况；
- 边界：0、负数、空容器；
- 错误情况：除零、越界。

### TDD 思想

先写测试，再写实现。今天先体会"代码写完配测试"就够。

## 核心重点
- assert 是最简单的测试
- 每个函数配一组测试
- 测正常、边界、错误
- 工业界用 gtest
- 测试是重构的底气

## 练习/任务
1. 给 Day8 的 add/max/isPrime 写 assert 测试
2. 写一个测试函数覆盖正常、边界、异常输入
3. 故意让一个 assert 失败，观察输出
4. （选做）查 Google Test 文档，看一个简单例子

## 今日刷题

- **LeetCode 200. 岛屿数量**。
- **LeetCode 130. 被围绕的区域**：DFS。
- **LeetCode 79. 单词搜索**：DFS 回溯。

## 打卡清单
- [ ] 已会用 assert 写测试
- [ ] 已给至少一个函数写测试
- [ ] 已理解测试边界条件
- [ ] 已完成至少一道搜索题
