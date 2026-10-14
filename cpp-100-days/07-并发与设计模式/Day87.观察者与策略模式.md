# Day 87 · 观察者与策略模式

## 今日目标
- 学会观察者模式：发布-订阅
- 学会策略模式：算法可替换
- 理解它们在工程中的应用

## 学习内容

### 观察者模式

一个对象状态变化时，自动通知所有订阅者。典型场景：事件系统、UI 通知。

```cpp
#include <iostream>
#include <vector>
#include <functional>

class EventSource {
    std::vector<std::function<void(const std::string&)>> listeners_;
public:
    void subscribe(std::function<void(const std::string&)> fn) {
        listeners_.push_back(fn);
    }
    void publish(const std::string& msg) {
        for (auto& fn : listeners_) fn(msg);
    }
};

int main() {
    EventSource src;
    src.subscribe([](const std::string& m){ std::cout << "A 收到：" << m << '\n'; });
    src.subscribe([](const std::string& m){ std::cout << "B 收到：" << m << '\n'; });
    src.publish("hello");
}
```

这里用 `std::function` 装 lambda，比传统接口更简洁。

### 策略模式

把"算法"做成可替换的组件：

```cpp
class Sorter {
public:
    virtual ~Sorter() = default;
    virtual void sort(std::vector<int>& v) = 0;
};

class BubbleSort : public Sorter {
public:
    void sort(std::vector<int>& v) override { /* 冒泡 */ }
};
class QuickSort : public Sorter {
public:
    void sort(std::vector<int>& v) override { /* 快排 */ }
};

class Context {
    Sorter* sorter_;
public:
    void setSorter(Sorter* s) { sorter_ = s; }
    void doSort(std::vector<int>& v) { sorter_->sort(v); }
};
```

C++ 里更常用 lambda 代替策略类：

```cpp
void process(std::function<void(int)> strategy) {
    for (int i = 0; i < 10; ++i) strategy(i);
}
```

### 两个模式的核心

- 观察者：一对多通知；
- 策略：算法可替换。

都用到"面向接口 + 多态"。

## 核心重点
- 观察者：状态变化自动通知订阅者
- 策略：算法可替换
- std::function 让 C++ 实现这两个模式更简洁
- 都建立在多态之上
- 别为模式而模式，问题出现了再用

## 练习/任务
1. 写一个 EventSource，订阅者用 lambda
2. 写一个排序策略接口，实现冒泡和快速排序两个策略
3. 用 lambda 代替策略类实现同样功能
4. （选做）写一个简单的按钮类，点击时通知所有订阅者

## 今日刷题

- **LeetCode 200. 岛屿数量**。
- **LeetCode 133. 克隆图**。
- **LeetCode 207. 课程表**。

## 打卡清单
- [ ] 已会用 std::function 实现观察者
- [ ] 已理解策略模式
- [ ] 已能用 lambda 简化策略
- [ ] 已完成至少一道图论题
