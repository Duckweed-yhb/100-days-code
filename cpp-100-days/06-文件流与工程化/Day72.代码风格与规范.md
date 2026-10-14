# Day 72 · 代码风格与规范

## 今日目标
- 了解主流 C++ 代码风格
- 养成良好命名与注释习惯
- 学会用编译器警告

## 学习内容

### 命名约定

| 元素 | 推荐风格 |
|---|---|
| 变量 | `studentCount`（小驼峰） |
| 函数 | `getAge()`（小驼峰） |
| 类 | `Student`（大驼峰） |
| 常量 | `MAX_SIZE`（全大写下划线）或 `constexpr` |
| 私有成员 | `name_`（下划线后缀） |

不同公司规范不同，**一个项目内统一即可**。

### 缩进与空格

- 4 空格缩进，不用 Tab；
- 大括号 K&R 风格或 Allman 风格，统一即可；
- 运算符两边加空格：`a + b`。

### 头文件顺序

```cpp
#include "your_header.h"     // 对应头文件先
#include <iostream>          // C++ 标准库
#include <vector>
#include <cstdio>            // C 标准库
```

### 编译器警告

开高警告：

```powershell
g++ -Wall -Wextra -Wpedantic ...
```

警告往往藏着 bug，别忽略。

### 现代 C++ 风格要点

- 用 `auto` 但不过度；
- 用智能指针，不用裸 new；
- 用 `const` 保护一切不变量；
- 用 `enum class` 代替裸 enum；
- 用 `nullptr` 不用 NULL。

### 注释写什么

- 为什么这么做（设计决策）；
- 复杂代码的思路；
- 公共 API 的用法示例。

别写"这行做了什么"——代码本身已经说明了。

## 核心重点
- 命名风格项目内统一
- 私有成员加 `_` 后缀
- 开 -Wall -Wextra
- const 到处加
- 注释解释 why，不是 what

## 练习/任务
1. 用统一风格重写你最近写的一个文件
2. 给编译命令加 -Wall -Wextra，把警告清零
3. 检查你的代码：有没有裸 new、有没有该 const 没 const
4. （选做）找一份 Google C++ Style Guide 读一读

## 今日刷题

- **LeetCode 200. 岛屿数量**：DFS/BFS。
- **LeetCode 994. 腐烂的橘子**：多源 BFS。
- **LeetCode 695. 岛屿的最大面积**：DFS。

## 打卡清单
- [ ] 已统一命名风格
- [ ] 已开高警告
- [ ] 已检查 const 使用
- [ ] 已完成至少一道搜索题
