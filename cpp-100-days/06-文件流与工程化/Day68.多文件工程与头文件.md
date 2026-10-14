# Day 68 · 多文件工程与头文件

## 今日目标
- 把代码拆成 .h / .cpp
- 理解声明与定义分离
- 学会用头文件卫士

## 学习内容

### 为什么要拆文件

一个 main.cpp 写到 1000 行没法维护。工程上：

- `.h` 头文件：放声明（类、函数原型）；
- `.cpp` 源文件：放定义。

### 例子：把加法拆出去

`add.h`：

```cpp
#ifndef ADD_H
#define ADD_H

int add(int a, int b);

#endif
```

`add.cpp`：

```cpp
#include "add.h"
int add(int a, int b) { return a + b; }
```

`main.cpp`：

```cpp
#include <iostream>
#include "add.h"

int main() {
    std::cout << add(1, 2);
}
```

编译：

```powershell
g++ main.cpp add.cpp -o app.exe -std=c++17
```

### 头文件卫士

`#ifndef ADD_H / #define ADD_H / #endif` 防止头文件被重复包含。新写法也可以用：

```cpp
#pragma once
```

Windows/MSVC/g++ 都支持 `#pragma once`，更简洁。

### 类的拆分

`student.h`：

```cpp
#ifndef STUDENT_H
#define STUDENT_H
#include <string>

class Student {
    std::string name_;
public:
    Student(const std::string& name);
    void introduce() const;
};
#endif
```

`student.cpp`：

```cpp
#include "student.h"
#include <iostream>

Student::Student(const std::string& name) : name_(name) {}
void Student::introduce() const {
    std::cout << name_ << '\n';
}
```

类外定义成员函数要写 `类名::函数名`。

### 模板为什么不能拆

模板代码必须在每个用到它的编译单元里可见，所以**模板通常全写在头文件里**，不能像普通函数那样放 .cpp。

## 核心重点
- .h 放声明，.cpp 放定义
- 头文件卫士 `#ifndef / #pragma once`
- 类外定义写 `类名::函数名`
- 编译时把所有 .cpp 一起传给 g++
- 模板写头文件里

## 练习/任务
1. 把之前写的 Student 类拆成 .h 和 .cpp
2. 写一个 math_utils.h/cpp，含 add/max/isPrime
3. 在 main.cpp 里 #include 并调用
4. （选做）故意不用头文件卫士，两次 include 同一个头文件，观察报错

## 今日刷题

- **LeetCode 206. 反转链表**：三指针。
- **LeetCode 92. 反转链表 II**：区间反转。
- **LeetCode 25. K 个一组翻转链表**：Hard。

## 打卡清单
- [ ] 已会写 .h/.cpp 分离
- [ ] 已会用头文件卫士
- [ ] 已会编译多文件工程
- [ ] 已完成至少一道链表题
