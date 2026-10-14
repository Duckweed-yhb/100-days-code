# Day 25 · 类与对象阶段项目

## 今日目标
- 综合运用本阶段所学，写一个完整的"学生成绩管理"类
- 练习封装、构造、析构、运算符重载、static、组合
- 第一次开始刷算法题

## 学习内容

### 项目：学生成绩管理类

需求：

- 一个 `Student` 类：姓名、学号、三科成绩；
- 一个 `GradeBook` 类：内部保存多个 Student，能增删查、按平均分排序；
- 重载 `<<` 打印学生信息；
- 用 static 统计当前学生总数。

### 参考骨架

```cpp
#include <iostream>
#include <string>
#include <vector>
#include <algorithm>

class Student {
    std::string name_;
    int id_;
    double scores_[3];
public:
    static int totalCount;

    Student(std::string name, int id, double s0, double s1, double s2)
        : name_(std::move(name)), id_(id) {
        scores_[0] = s0; scores_[1] = s1; scores_[2] = s2;
        ++totalCount;
    }
    ~Student() { --totalCount; }

    double average() const {
        return (scores_[0] + scores_[1] + scores_[2]) / 3.0;
    }
    const std::string& name() const { return name_; }
    int id() const { return id_; }
};

int Student::totalCount = 0;

std::ostream& operator<<(std::ostream& os, const Student& s) {
    return os << s.name() << "(id=" << s.id() << ", 平均分=" << s.average() << ")";
}

class GradeBook {
    std::vector<Student> students_;
public:
    void add(const Student& s) { students_.push_back(s); }
    void printAll() const {
        for (const auto& s : students_) std::cout << s << '\n';
    }
    void sortByAverage() {
        std::sort(students_.begin(), students_.end(),
            [](const Student& a, const Student& b) {
                return a.average() > b.average();
            });
    }
};
```

> `std::vector`、`std::sort`、lambda 我们后面会专门学，今天先照抄能跑即可。

### 复盘清单

本阶段你应该已经掌握：

- class / struct、public/private/protected；
- 构造函数、初始化列表、析构函数；
- 拷贝构造、拷贝赋值、三/五法则；
- 运算符重载（成员 + 全局）；
- static、friend、this；
- 对象生命周期、RAII 思想、组合。

## 核心重点
- 面向对象 = 数据 + 操作数据的函数捆在一起
- 封装：私有成员 + 公开接口
- 资源型类要写析构、拷贝构造、拷贝赋值
- 组合比继承更灵活
- 下一阶段开始学继承与多态

## 练习/任务
1. 把上面的 GradeBook 补全：能按学号查找学生、删除学生
2. 给 Student 加一个 `grade()` 方法，根据平均分返回 A/B/C/D
3. 写 main 创建 5 个学生，按平均分排序打印
4. （选做）统计并打印 `Student::totalCount`

## 今日刷题

- **LeetCode 1603. 设计一个停车场系统**：用类封装一个停车场，按车型分配车位。思路：写 `ParkingSystem` 类，内部存三种车型剩余车位数，`addCar` 时对应车位减一并返回是否成功。
- **LeetCode 225. 用队列实现栈**：用两个队列模拟栈的 push/pop/top/empty。思路：两个 `std::queue`，push 时往有数据的队列塞，pop 时把前 n-1 个搬到另一个队列，剩下的就是栈顶。
- **LeetCode 705. 设计哈希集合**：手写 `MyHashSet`，内部用桶 + 链表。思路：取模定桶，桶里用 `std::list<int>` 存元素，add/remove/contains 时先定位桶再遍历。

## 打卡清单
- [ ] 已完成 GradeBook 并跑通增删查改
- [ ] 已实现按平均分排序
- [ ] 已完成至少一道今日刷题
- [ ] 已复盘本阶段所有知识点
