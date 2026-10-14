# Day 21 · friend友元

## 今日目标
- 理解友元的作用与代价
- 学会把函数或类声明为友元
- 知道什么时候才该用 friend

## 学习内容

### 为什么需要友元

封装要求成员私有，但有时候两个类关系特别紧密，需要互相访问内部细节。C++ 提供 `friend` 关键字：授权某个外部函数或类访问本类的 private 成员。

### 友元函数

```cpp
class Box {
    double width_;
public:
    Box(double w) : width_(w) {}
    friend void printWidth(const Box& b);    // 声明这是友元
};

void printWidth(const Box& b) {
    std::cout << b.width_ << '\n';           // 直接访问 private
}
```

注意：

- `friend` 声明在类里，位置不限（public/private 都行）；
- 友元函数不是成员函数，没有 this，不能用 `对象.` 调。

### 友元类

整个类都可以是友元：

```cpp
class Engine;   // 前向声明

class Car {
    int speed_;
    friend class Engine;       // Engine 可以访问 Car 的 private
};

class Engine {
public:
    void tune(Car& c) { c.speed_ = 200; }   // 直接访问
};
```

### 友元的性质

- 友元关系**不对称**：Car 说 Engine 是朋友，不代表 Car 是 Engine 的朋友；
- 友元关系**不能继承**、**不能传递**；
- 友元**破坏封装**，能不用就不用。

### 典型用途

- 重载流运算符 `operator<<`：它必须是全局函数，但又要访问私有成员；
- 两个紧密协作的类（如容器与迭代器）；
- 数学库中两个类互相访问内部表示。

### 反面教材

不要为了图方便把整个类设为友元，那等于把 private 全公开。

## 核心重点
- friend 授权外部函数/类访问 private 成员
- 友元函数不是成员函数
- 友元关系不对称、不传递、不继承
- 主要用于 operator<< 和紧密协作的类
- 友元破坏封装，谨慎使用

## 练习/任务
1. 把 Day18 Complex 的 operator<< 改成访问私有成员的友元函数
2. 写一个 `TV` 类和 `Remote` 类，Remote 作为 TV 的友元，可以调 TV 的 private 方法
3. 写一个类，声明一个外部函数为友元，验证它能访问私有成员
4. （选做）故意把友元关系写反（A 声明 B 是朋友，B 访问 A 的私有没问题；A 访问 B 的私有失败），观察编译错误

## 打卡清单
- [ ] 已会写 friend 函数声明
- [ ] 已会写 friend class
- [ ] 已理解友元不对称
- [ ] 已知道 operator<< 常用友元实现
