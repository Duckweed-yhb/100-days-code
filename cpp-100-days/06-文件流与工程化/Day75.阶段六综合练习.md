# Day 75 · 阶段六综合练习

## 今日目标
- 综合运用文件流、多文件工程、CMake、单元测试
- 把之前的通讯录项目做成一个真正的小工程
- 复盘本阶段

## 学习内容

### 本阶段知识地图

| 主题 | 关键 |
|---|---|
| 文件流 | ofstream/ifstream、ios::binary、seekg |
| 字符串流 | ostringstream/istringstream |
| 多文件 | .h/.cpp、头文件卫士 |
| 编译链接 | ODR、声明与定义 |
| 命名空间 | 分层、匿名命名空间 |
| CMake | CMakeLists.txt、add_executable |
| 代码风格 | 命名、const、警告 |
| 单元测试 | assert、gtest |
| 调试 | -g、gdb、VS |

### 综合项目：持久化通讯录

需求：

- 用 Day50 的 ContactBook；
- 程序启动时从 contacts.csv 加载；
- 退出时保存回 contacts.csv；
- 拆成 contact.h / contact.cpp / main.cpp；
- 写几个 assert 测试。

文件结构：

```
contactbook/
  CMakeLists.txt
  include/contact.h
  src/contact.cpp
  src/main.cpp
  tests/test_contact.cpp
```

### CMakeLists.txt 参考

```cmake
cmake_minimum_required(VERSION 3.10)
project(contactbook)
set(CMAKE_CXX_STANDARD 17)
add_executable(contactbook src/main.cpp src/contact.cpp)
target_include_directories(contactbook PRIVATE include)

add_executable(test_contact tests/test_contact.cpp src/contact.cpp)
target_include_directories(test_contact PRIVATE include)
```

### 复盘建议

- 从今天起，写代码都按"头文件 + 源文件 + CMake"结构；
- 给关键函数配 assert 测试；
- 下一阶段进入并发与设计模式。

## 核心重点
- 工程化 = 多文件 + CMake + 测试 + 风格
- 文件流做持久化
- 命名空间组织代码
- 调试器是程序员的第二双眼睛

## 练习/任务
1. 把 ContactBook 拆成 .h/.cpp
2. 加 CSV 加载/保存
3. 写 CMakeLists.txt 构建
4. （选做）写一个 test_contact.cpp，测试 add/find/remove

## 今日刷题

- **LeetCode 200. 岛屿数量**。
- **LeetCode 994. 腐烂的橘子**。
- **LeetCode 207. 课程表**：拓扑排序。

## 打卡清单
- [ ] 已完成多文件 ContactBook
- [ ] 已写 CMakeLists.txt
- [ ] 已加 CSV 持久化
- [ ] 已写至少一组单元测试
