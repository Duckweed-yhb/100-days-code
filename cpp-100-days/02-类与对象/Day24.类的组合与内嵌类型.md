# Day 24 · 类的组合与内嵌类型

## 今日目标
- 理解"组合"：对象里嵌套对象
- 学会用初始化列表初始化成员对象
- 了解类中定义嵌套类型的写法

## 学习内容

### 组合：把对象当成员

现实中一个类常常由其他类组成：一辆车有引擎、轮胎、方向盘。这就是**组合（composition）**：

```cpp
class Engine {
public:
    void start() { std::cout << "引擎启动\n"; }
};

class Car {
    Engine engine_;        // Car 里"有一个" Engine
    std::string model_;
public:
    Car(const std::string& m) : model_(m) {}
    void start() { engine_.start(); }
};
```

### 成员对象的初始化

成员对象如果没有默认构造函数，必须在初始化列表里显式构造：

```cpp
class Engine {
    int hp_;
public:
    Engine(int hp) : hp_(hp) {}
};

class Car {
    Engine engine_;
public:
    Car(int hp) : engine_(hp) {}    // 必须在初始化列表里构造 Engine
};
```

如果忘了写初始化列表，编译器会尝试调 `Engine()` 默认构造函数；Engine 没有默认构造就报错。

### 构造析构顺序回顾

组合情况下：

- 构造：先成员对象（按声明顺序），再自身；
- 析构：先自身，再成员对象（逆序）。

### 嵌套类型

可以在类内部定义另一个类/结构体：

```cpp
class Stack {
    struct Node { int value; Node* next; };   // 嵌套类型
    Node* top_ = nullptr;
public:
    ~Stack() { while (top_) { Node* p = top_; top_ = top_->next; delete p; } }
    void push(int v) { top_ = new Node{v, top_}; }
};
```

嵌套类型对外部是隐藏的，只在类内部有用，常用来实现"内部数据结构"。

### 组合 vs 继承（预告）

- 组合："有一个"（Car has an Engine）；
- 继承："是一个"（Dog is an Animal，Day26 讲）。

能用组合就别用继承，更灵活、耦合更低。

## 核心重点
- 组合 = 把其他类对象作为成员
- 成员对象没有默认构造时必须在初始化列表里构造
- 构造先成员后自身，析构逆序
- 类里可以定义嵌套类型，对外隐藏实现细节
- 优先用组合而非继承

## 练习/任务
1. 设计 `Address`（省、市、街道）和 `Person`（姓名、Address）两个类，Person 组合 Address
2. 给 Car 类加 `Engine` 和 `Wheel` 两个成员对象，写构造函数用初始化列表
3. 在 Stack 类内部定义 Node 结构体，实现 push/pop
4. （选做）给组合类加打印函数，观察构造析构顺序

## 打卡清单
- [ ] 已会在类里嵌入其他类对象
- [ ] 已会用初始化列表构造成员对象
- [ ] 已理解构造析构顺序
- [ ] 已会在类里定义嵌套类型
