# Day 71 · CMake入门

## 今日目标
- 理解为什么需要构建系统
- 学会写最简单的 CMakeLists.txt
- 用 CMake 构建一个多文件项目

## 学习内容

### 为什么需要 CMake

项目文件多了，每次手敲 `g++ a.cpp b.cpp c.cpp -o app` 太累。CMake 是跨平台构建工具：写一份 CMakeLists.txt，它生成 Makefile 或 VS 工程。

### 最小 CMakeLists.txt

```cmake
cmake_minimum_required(VERSION 3.10)
project(myapp)

set(CMAKE_CXX_STANDARD 17)
set(CMAKE_CXX_STANDARD_REQUIRED ON)

add_executable(myapp main.cpp add.cpp student.cpp)
```

### 构建步骤

```powershell
mkdir build
cd build
cmake ..
cmake --build .
```

- `cmake ..`：根据上一级目录的 CMakeLists.txt 生成构建文件；
- `cmake --build .`：真正编译。

Windows 上会生成 `.sln`，可以用 Visual Studio 打开。

### 加头文件目录

如果头文件在 `include/`：

```cmake
target_include_directories(myapp PRIVATE include)
```

### 编译选项

```cmake
set(CMAKE_CXX_FLAGS "-Wall -Wextra")
```

### 库

把代码做成库：

```cmake
add_library(mymath add.cpp sub.cpp)
add_executable(myapp main.cpp)
target_link_libraries(myapp PRIVATE mymath)
```

## 核心重点
- CMakeLists.txt 描述项目，真正构建由它生成
- 四件套：cmake_minimum_required / project / set CXX_STANDARD / add_executable
- 构建在 build/ 目录里做，别污染源码
- 库用 add_library + target_link_libraries
- 头文件目录用 target_include_directories

## 练习/任务
1. 给 Day68 的多文件项目写一个 CMakeLists.txt
2. 用 cmake 构建并运行
3. 加 -Wall -Wextra 编译选项
4. （选做）把 math_utils 做成静态库，主程序链接它

## 今日刷题

- **LeetCode 704. 二分查找**。
- **LeetCode 34. 在排序数组中查找元素的第一个和最后一个位置**：二分左右边界。
- **LeetCode 33. 搜索旋转排序数组**：二分变形。

## 打卡清单
- [ ] 已会写最小 CMakeLists.txt
- [ ] 已能 cmake 构建多文件项目
- [ ] 已会加头文件目录
- [ ] 已完成至少一道二分题
