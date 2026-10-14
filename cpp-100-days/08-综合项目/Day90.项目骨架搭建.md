# Day 90 · 项目骨架搭建

## 今日目标
- 建好目录结构与 CMakeLists.txt
- 让 main.cpp 能编译运行
- 搭好空模块文件

## 学习内容

### 目录结构

```
todo/
  CMakeLists.txt
  include/
    task.h
    store.h
    persistence.h
  src/
    task.cpp
    store.cpp
    persistence.cpp
    main.cpp
  tests/
    test.cpp
```

### CMakeLists.txt

```cmake
cmake_minimum_required(VERSION 3.10)
project(todo)
set(CMAKE_CXX_STANDARD 17)
set(CMAKE_CXX_STANDARD_REQUIRED ON)

add_executable(todo
    src/main.cpp
    src/task.cpp
    src/store.cpp
    src/persistence.cpp)
target_include_directories(todo PRIVATE include)

add_executable(test_todo
    tests/test.cpp
    src/task.cpp
    src/store.cpp
    src/persistence.cpp)
target_include_directories(test_todo PRIVATE include)
```

### task.h 参考

```cpp
#ifndef TODO_TASK_H
#define TODO_TASK_H
#include <string>

struct Task {
    int id = 0;
    std::string title;
    int priority = 3;
    bool done = false;
    std::string due;
};

#endif
```

### main.cpp 先空跑

```cpp
#include <iostream>
int main() {
    std::cout << "TODO manager v0.1\n";
    return 0;
}
```

### 验证

```powershell
mkdir build
cd build
cmake ..
cmake --build .
.\todo.exe
```

能看到 `TODO manager v0.1` 即骨架完成。

## 核心重点
- 先让空项目编译跑通，再填肉
- CMakeLists.txt 一次写好
- 头文件卫士不能少
- 小步提交

## 练习/任务
1. 建好上面的目录结构
2. 写好 CMakeLists.txt
3. 让 todo.exe 打印欢迎信息
4. （选做）把 test_todo 也编译出来

## 今日刷题

- **LeetCode 200. 岛屿数量**。
- **LeetCode 133. 克隆图**。
- **LeetCode 207. 课程表**。

## 打卡清单
- [ ] 已建好目录结构
- [ ] 已写好 CMakeLists.txt
- [ ] 已能编译运行空 main
- [ ] 已写好 task.h
