# Day 34 · 组合与继承的取舍

## 今日目标
- 理解什么时候用继承、什么时候用组合
- 掌握"is-a"与"has-a"的判断
- 了解里氏替换原则

## 学习内容

### 两个经典关系

- **is-a（是一个）**：Dog is an Animal → 用继承；
- **has-a（有一个）**：Car has an Engine → 用组合。

```cpp
// 继承：Dog 是一种 Animal
class Dog : public Animal {};

// 组合：Car 里装着 Engine
class Car { Engine engine_; };
```

### 什么时候不要用继承

新手喜欢"为了复用代码"就继承：

```cpp
class Stack : public std::vector<int> {   // 不好！
    // ...
};
```

问题：继承暴露了基类所有接口，用户可以 `stack.push_back` 然后乱搞，破坏栈的语义。

更好的做法是组合：

```cpp
class Stack {
    std::vector<int> data_;
public:
    void push(int x) { data_.push_back(x); }
    int  pop() { int v = data_.back(); data_.pop_back(); return v; }
};
```

### 里氏替换原则（LSP）

**派生类必须能替换基类，而不破坏程序正确性。**

经典反例：正方形继承矩形：

```cpp
class Rectangle {
public:
    virtual void setWidth(int w);
    virtual void setHeight(int h);
};
class Square : public Rectangle {
    void setWidth(int w) override { Rectangle::setWidth(w); Rectangle::setHeight(w); }
};
```

外部代码 `r.setWidth(5); r.setHeight(10); assert(r.area() == 50);` 对矩形成立，换成正方形就崩了——正方形不能替换矩形，继承关系就是错的。

### 经验法则

- 优先组合：更灵活、运行时可换、不破坏封装；
- 只有真正满足 is-a + LSP 时才用继承；
- 继承主要为了多态，而不是为了代码复用。

## 核心重点
- is-a 用继承，has-a 用组合
- 不要为了复用代码而继承
- LSP：派生类必须能替换基类
- 组合比继承耦合更低、更灵活
- 继承的主要目的是多态

## 练习/任务
1. 用组合实现一个 Stack（内部用 vector）
2. 设计一个 `MessageQueue` 类，组合 `std::list` 而不是继承它
3. 分析："汽车与引擎"、"学生与人"、"矩形与正方形"哪些该继承、哪些该组合
4. （选做）找一个你写过的类，看看能不能用组合替代继承

## 今日刷题

- **LeetCode 206. 反转链表**：三指针 pre/cur/next，逐个翻转指向。
- **LeetCode 21. 合并两个有序链表**：哑节点 + 双指针，每次取较小者接到结果后面。
- **LeetCode 141. 环形链表**：快慢指针，快指针追上慢指针即有环。

## 打卡清单
- [ ] 已会用组合包装容器
- [ ] 已理解 LSP
- [ ] 已能判断该继承还是该组合
- [ ] 已完成至少一道链表刷题
