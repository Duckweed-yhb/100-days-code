# Day 14 · 类与结构体入门

## 今日目标
- 理解为什么需要类：把数据和操作捆在一起
- 学会定义最简单的 class
- 区分 struct 与 class 的默认访问权限

## 学习内容

### 从面向过程到面向对象

前面我们写程序，数据和操作是分开的：

```cpp
double r = 1.0;
double area = 3.14159 * r * r;
```

当系统变大，"圆"可能有半径、圆心、求面积、求周长、画圆……这些数据和操作散在各处，很难维护。**类（class）** 就是把相关的数据和函数打包成一个新类型。

### 第一个类

```cpp
#include <iostream>
#include <string>

class Student {
public:                          // 以下成员对外可见
    std::string name;
    int age;

    void introduce() {          // 成员函数
        std::cout << "我叫" << name << "，今年" << age << "岁\n";
    }
};

int main() {
    Student s;
    s.name = "余泓彬";
    s.age = 20;
    s.introduce();
}
```

要点：

- `class 类名 { ... };`，注意花括号后有分号；
- 类里的变量叫**成员变量**，函数叫**成员函数**；
- 用 `.` 访问对象的成员。

### 对象的创建

```cpp
Student s1;                  // 栈上创建
Student s2 = {"张三", 19};   // C++11 列表初始化（按声明顺序）
Student* p = new Student;    // 堆上创建
p->introduce();              // 指针用 -> 访问
delete p;
```

- 对象在栈上：自动销毁；
- 对象在堆上：`new` 出来，要 `delete`。

### struct 与 class 的区别

C++ 里 `struct` 几乎就是 `class`，唯一区别是默认访问权限：

| 关键字 | 默认成员权限 | 默认继承权限 |
|---|---|---|
| `struct` | public | public |
| `class` | private | private |

习惯：**只装数据的"胖结构体"用 struct**，有行为、要封装的用 class。

### 成员函数的调用

成员函数可以直接访问同类的成员变量，不需要 `对象名.`：

```cpp
class Student {
public:
    std::string name;
    void greet(const std::string& other) {
        std::cout << name << " 向 " << other << " 问好\n";
    }
};
```

## 核心重点
- 类把数据和操作这些数据的函数打包成新类型
- `class 类名 { public: ... };` 末尾要分号
- 用 `.` 访问栈对象，用 `->` 访问指针对象
- struct 默认 public，class 默认 private
- 成员函数可直接访问同类成员变量

## 练习/任务
1. 定义一个 `Circle` 类，含半径成员，写 `area()` 和 `circumference()` 两个成员函数
2. 定义一个 `Book` 类，含书名、作者、价格，写 `printInfo()` 打印信息
3. 在 main 中创建两个 Book 对象并打印
4. （选做）把 Book 改成 struct，对比默认访问权限的差异

## 打卡清单
- [ ] 已会定义最简单的 class
- [ ] 已会创建对象并调用成员函数
- [ ] 已分清 . 与 -> 的使用场景
- [ ] 已知道 struct 与 class 的默认权限差异
