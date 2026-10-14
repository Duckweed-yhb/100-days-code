# Day 30 · 多态的应用

## 今日目标
- 学会用基类指针/引用管理一族对象
- 理解多态在工程中的典型用法
- 区分"覆盖"与"重载"

## 学习内容

### 多态是什么

同一段代码，处理不同类型的对象时，自动表现出不同行为。

```cpp
class Shape {
public:
    virtual double area() const = 0;     // 纯虚函数，Day31 讲
    virtual ~Shape() = default;
};

class Circle : public Shape {
    double r_;
public:
    Circle(double r) : r_(r) {}
    double area() const override { return 3.14159 * r_ * r_; }
};

class Rectangle : public Shape {
    double w_, h_;
public:
    Rectangle(double w, double h) : w_(w), h_(h) {}
    double area() const override { return w_ * h_; }
};

void printArea(const Shape& s) {
    std::cout << s.area() << '\n';
}

int main() {
    Circle c(1);
    Rectangle r(2, 3);
    printArea(c);    // 3.14
    printArea(r);    // 6
}
```

`printArea` 不关心传进来的是什么形状，只要它是 Shape 就行。这就是多态的威力。

### 多态的三个条件

1. **继承**：派生类自基类；
2. **重写**：派生类重写基类的虚函数；
3. **基类指针/引用调用**：用 `Shape*` 或 `Shape&` 调虚函数。

### 覆盖 vs 重载 vs 隐藏

| 概念 | 发生位置 | 条件 |
|---|---|---|
| 重载 | 同一作用域 | 同名不同参数列表 |
| 覆盖（重写） | 父子类之间 | 基类 virtual + 派生类同签名 |
| 隐藏 | 父子类之间 | 派生类同名，不管签名 |

### 工程中的典型用法

- 图形系统：Shape 一族；
- UI 控件：Widget 一族（Button、TextBox）；
- 游戏实体：Entity 一族（Player、Enemy）；
- 网络消息：Message 一族（Login、Logout、Chat）。

只要"一批类做同一件事，但实现不同"，就该想到多态。

## 核心重点
- 多态 = 基类接口 + 不同派生类实现
- 三条件：继承、重写虚函数、基类指针/引用调用
- 覆盖、重载、隐藏三者别混
- 多态让代码对扩展开放、对修改关闭（设计原则）

## 练习/任务
1. 扩展 Shape 体系：加 Triangle，写一个 `printAllAreas(const vector<Shape*>&)`
2. 写 `Employee` 基类（虚函数 salary()），派生出 FullTime 和 Intern，计算工资总和
3. 写 `Drawable` 基类（虚函数 draw()），派生 Line/Circle/Rect
4. （选做）在 vector<Shape*> 里放不同派生类对象，循环调 area()

## 今日刷题

- **LeetCode 112. 路径总和**：给二叉树和一个目标和，判断是否存在根到叶子的路径使节点和等于目标。思路：递归，`hasPathSum(root, sum) = hasPathSum(root->left, sum-root->val) || hasPathSum(root->right, sum-root->val)`，叶子节点判断 `sum == root->val`。
- **LeetCode 104. 二叉树的最大深度**：递归 `maxDepth = 1 + max(left, right)`。
- **LeetCode 144. 二叉树的前序遍历**：用递归或栈模拟，顺序：根 -> 左 -> 右。

## 打卡清单
- [ ] 已写过多态数组的例子
- [ ] 已分清覆盖/重载/隐藏
- [ ] 已完成至少一道二叉树刷题
- [ ] 已能识别"该用多态"的场景
