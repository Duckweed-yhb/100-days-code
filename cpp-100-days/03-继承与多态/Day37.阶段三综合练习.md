# Day 37 · 阶段三综合练习

## 今日目标
- 把继承、虚函数、抽象类、多态串起来
- 设计一个小型多态体系
- 复盘本阶段易错点

## 学习内容

### 本阶段知识地图

| 主题 | 关键概念 |
|---|---|
| 继承 | public 继承、基类/派生类、构造析构顺序 |
| 名字隐藏 | 派生类同名函数隐藏基类，用 using 解锁 |
| 虚函数 | virtual、override、动态绑定 |
| 抽象类 | 纯虚函数 `= 0`，不能实例化 |
| 虚析构 | 基类析构必须 virtual |
| 多继承 | 菱形继承、virtual public、接口隔离 |
| 组合 vs 继承 | is-a vs has-a、LSP |
| 切片与 RTTI | 对象切片、dynamic_cast |

### 综合题目：图形计算器

需求：

- 抽象基类 `Shape`：纯虚函数 `area()`、`perimeter()`、`name()`；
- 派生类 `Circle`、`Rectangle`、`Triangle`；
- 一个 `ShapeCanvas` 类，内部用 `std::vector<std::unique_ptr<Shape>>` 存各种形状；
- 提供 `add(unique_ptr<Shape>)`、`totalArea()`、`printAll()`。

参考骨架：

```cpp
#include <iostream>
#include <memory>
#include <vector>
#include <cmath>

class Shape {
public:
    virtual double area() const = 0;
    virtual double perimeter() const = 0;
    virtual std::string name() const = 0;
    virtual ~Shape() = default;
};

class Circle : public Shape {
    double r_;
public:
    Circle(double r) : r_(r) {}
    double area() const override { return M_PI * r_ * r_; }
    double perimeter() const override { return 2 * M_PI * r_; }
    std::string name() const override { return "Circle"; }
};

// Rectangle、Triangle 自己补
```

> `std::unique_ptr` Day52 会细讲，今天先当"自动 delete 的智能指针"用。

### 复盘易错点

1. 基类析构忘了 virtual；
2. 派生类重写时签名写错，变成隐藏而非覆盖；
3. 多态函数按值传参导致切片；
4. 菱形继承忘了 virtual public；
5. 为了复用代码乱继承。

## 核心重点
- 多态 = 抽象基类 + 派生类重写 + 基类指针/引用
- 基类析构 virtual 是铁律
- 用组合优先于继承
- 下一阶段进入 STL 与泛型编程

## 练习/任务
1. 完成上面的 Shape 体系：Circle、Rectangle、Triangle
2. 写 ShapeCanvas，支持 add、totalArea、printAll
3. 在 main 中创建 5 个不同形状，打印总面积和周长
4. （选做）加一个 `scale(double k)` 虚函数，让所有形状能缩放

## 今日刷题

- **LeetCode 199. 二叉树的右视图**：层序 BFS，每层取最右节点。
- **LeetCode 230. 二叉搜索树中第 K 小的元素**：中序遍历是升序，走到第 k 个就返回。
- **LeetCode 98. 验证二叉搜索树**：中序遍历是否严格递增；或递归时带上 [low, high] 区间检查。

## 打卡清单
- [ ] 已完成 Shape 多态体系
- [ ] 已写 ShapeCanvas 并跑通
- [ ] 已完成至少一道树刷题
- [ ] 已复盘本阶段所有易错点
