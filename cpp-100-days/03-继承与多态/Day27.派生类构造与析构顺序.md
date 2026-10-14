# Day 27 · 派生类构造与析构顺序

## 今日目标
- 学会在派生类构造函数里调用基类构造函数
- 理解基类没有默认构造时的处理
- 梳理继承体系下的析构顺序

## 学习内容

### 派生类怎么调基类构造

如果基类有带参构造函数，派生类必须在初始化列表里显式调用它：

```cpp
class Animal {
protected:
    std::string name_;
public:
    Animal(std::string name) : name_(std::move(name)) {}
};

class Dog : public Animal {
    int age_;
public:
    Dog(std::string name, int age) : Animal(std::move(name)), age_(age) {}
    //                                ^^^^^^^^^^^^^^^^^^^^^^^^
    //                                显式调基类构造
};
```

### 为什么必须显式调

派生类对象创建时，先得把"继承来的那部分"构造好。如果基类没有默认构造函数，编译器不知道怎么帮你初始化那部分，就只能由你显式指定。

### 析构函数不能被显式调用

派生类析构不需要写 `~Derived() : ~Base()`，析构函数会**自动**调用基类析构：

```cpp
class Base {
public:
    ~Base() { std::cout << "~Base\n"; }
};
class Derived : public Base {
public:
    ~Derived() { std::cout << "~Derived\n"; }
};
// 销毁 Derived 对象：~Derived -> ~Base
```

### 完整构造顺序

```
1. 虚基类（Day33 讲，先不管）
2. 基类构造（按继承顺序）
3. 成员对象构造（按声明顺序）
4. 派生类构造函数体
```

析构顺序完全相反。

### 继承下的三法则

如果派生类有自己的资源（new 出来的东西），它要自己写析构、拷贝构造、拷贝赋值，并在里面调用基类对应版本：

```cpp
Derived& Derived::operator=(const Derived& o) {
    Base::operator=(o);       // 先调基类赋值
    // 再处理派生类自己的成员
    return *this;
}
```

## 核心重点
- 派生类必须在初始化列表里调基类构造
- 基类无默认构造时，这一步是必须的
- 析构自动调用基类析构，不要手动写
- 构造顺序：基类 -> 成员 -> 派生类体；析构相反
- 派生类拷贝函数要记得调基类版本

## 练习/任务
1. 给 Day26 的 Animal 加带参构造，让 Dog/Cat 显式调用
2. 在 Animal、Dog、Dog 的成员对象里都加打印日志，观察完整构造顺序
3. 写一个 Person 基类（姓名），派生 Student（学号），派生 Undergraduate（专业），三级继承
4. （选做）故意不在派生类初始化列表里调基类带参构造，观察编译错误

## 今日刷题

- **LeetCode 100. 相同的树**：递归比较两棵树结构与值。
- **LeetCode 101. 对称二叉树**：递归比较左右子树是否镜像。
- **LeetCode 226. 翻转二叉树**：交换每个节点的左右子树。

## 打卡清单
- [ ] 已会在派生类初始化列表里调基类构造
- [ ] 已理解基类无默认构造时的处理
- [ ] 已能预测三级继承的构造析构顺序
- [ ] 已知道派生类 operator= 要调基类版本
