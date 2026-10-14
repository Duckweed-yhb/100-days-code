# Day 31 · 纯虚函数与抽象类

## 今日目标
- 学会用 `= 0` 声明纯虚函数
- 理解抽象类不能实例化
- 用抽象类定义"接口"

## 学习内容

### 纯虚函数

在虚函数后面写 `= 0`，表示这个函数**没有默认实现**，派生类必须重写它：

```cpp
class Shape {
public:
    virtual double area() const = 0;     // 纯虚函数
    virtual double perimeter() const = 0;
    virtual ~Shape() = default;
};
```

含有纯虚函数的类叫**抽象类**。

### 抽象类不能实例化

```cpp
Shape s;              // 错：抽象类不能创建对象
Shape* p = new Circle(1);   // 对：可以定义指针/引用
```

抽象类的唯一用途就是当基类，规定"派生类必须提供这些接口"。

### 派生类必须重写所有纯虚函数

```cpp
class Circle : public Shape {
    double r_;
public:
    Circle(double r) : r_(r) {}
    double area() const override { return 3.14159 * r_ * r_; }
    double perimeter() const override { return 2 * 3.14159 * r_; }
};
```

只要有一个纯虚函数没重写，派生类也仍然是抽象类。

### 接口：只含纯虚函数的抽象类

当一个类**所有**成员函数都是纯虚函数、没有成员变量时，它就是"接口"：

```cpp
class Printable {
public:
    virtual std::string toString() const = 0;
    virtual ~Printable() = default;
};
```

任何类只要继承 Printable 并实现 toString，就可以被一个通用函数打印：

```cpp
void print(const Printable& p) {
    std::cout << p.toString() << '\n';
}
```

### 纯虚函数也能有实现

纯虚函数可以在类外给一个默认实现，但派生类仍必须重写（不过可以显式调用基类版本）：

```cpp
class Base {
public:
    virtual void f() = 0;
};
void Base::f() { std::cout << "默认实现\n"; }
```

## 核心重点
- 纯虚函数：`virtual 返回类型 函数名(参数) = 0;`
- 含纯虚函数的类是抽象类，不能实例化
- 派生类必须重写所有纯虚函数，否则仍是抽象类
- 纯虚函数组成"接口"，规定派生类必须提供的行为
- 抽象类只能当基类用

## 练习/任务
1. 把 Day30 的 Shape 改成抽象类（area/perimeter 都是纯虚）
2. 定义接口 `Drawable { virtual void draw() = 0; }`，让 Circle/Rectangle 实现
3. 写一个抽象类 `Logger`，派生 FileLogger 和 ConsoleLogger
4. （选做）尝试 `Shape s;`，观察编译错误

## 今日刷题

- **LeetCode 101. 对称二叉树**：判断一棵二叉树是否镜像对称。思路：写辅助函数 `isMirror(a, b)`，比较 a->val == b->val 且 `isMirror(a->left, b->right) && isMirror(a->right, b->left)`。
- **LeetCode 110. 平衡二叉树**：判断是否高度平衡。思路：后序遍历，返回子树高度，同时发现左右高度差 > 1 就标记不平衡。
- **LeetCode 108. 将有序数组转换为二叉搜索树**：取中点作根，左右递归构造。

## 打卡清单
- [ ] 已会写纯虚函数
- [ ] 已理解抽象类不能实例化
- [ ] 已会用接口规定行为
- [ ] 已完成至少一道树刷题
