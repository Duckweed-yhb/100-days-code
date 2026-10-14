# Day 28 · 名字隐藏与继承同名函数

## 今日目标
- 理解派生类与基类同名函数时的"名字隐藏"
- 学会用 `using` 把基类名字引入派生类作用域
- 避免继承中常见的函数签名陷阱

## 学习内容

### 名字隐藏现象

如果派生类定义了一个和基类**同名**的函数（哪怕参数不同），基类所有同名版本都会被隐藏：

```cpp
class Base {
public:
    void f(int)   { std::cout << "Base::f(int)\n"; }
    void f(double){ std::cout << "Base::f(double)\n"; }
};

class Derived : public Base {
public:
    void f(const std::string&) { std::cout << "Derived::f(string)\n"; }
};

int main() {
    Derived d;
    d.f(1);        // 编译错误！Base::f(int) 被隐藏
    d.f("hi");     // 调 Derived::f(string)
}
```

这不是重载——派生类和基类是不同作用域，编译器在 Derived 里找到 `f(string)` 后就不再去 Base 里找了。

### 用 using 解锁基类版本

```cpp
class Derived : public Base {
public:
    using Base::f;          // 把 Base 的所有 f 引入当前作用域
    void f(const std::string&) { std::cout << "Derived::f\n"; }
};

int main() {
    Derived d;
    d.f(1);         // OK：Base::f(int)
    d.f(2.5);       // OK：Base::f(double)
    d.f("hi");      // OK：Derived::f(string)
}
```

### 不要在继承中玩"签名不同就重载"

新手常以为：基类写了 `f(int)`，派生类写 `f(double)` 就是重载。实际上是隐藏。要么用 using 引入，要么别起同名。

### 重名成员变量

同理：派生类定义了和基类同名的成员变量，会隐藏基类的那个。访问时要加 `基类::`：

```cpp
class Base { public: int x = 1; };
class Derived : public Base { public: int x = 2; };

Derived d;
std::cout << d.x;          // 2（Derived 的）
std::cout << d.Base::x;    // 1（Base 的）
```

## 核心重点
- 派生类与基类同名函数会"隐藏"基类版本，不论参数是否相同
- 这不是重载
- 用 `using 基类::函数名;` 把基类版本重新引入
- 同名成员变量也会隐藏，要用 `基类::成员` 访问
- 写继承体系时函数名尽量别乱重

## 练习/任务
1. 复现上面的名字隐藏例子，观察编译错误
2. 用 using 修复，让 d.f(1) 能调到 Base::f(int)
3. 写基类 `Shape` 有 `draw()`，派生 `Circle` 也写 `draw()`，验证隐藏行为
4. （选做）在派生类里访问被隐藏的基类成员变量

## 今日刷题

- **LeetCode 94. 二叉树的中序遍历**：递归或栈。
- **LeetCode 144. 二叉树的前序遍历**：根 -> 左 -> 右。
- **LeetCode 145. 二叉树的后序遍历**：左 -> 右 -> 根。

## 打卡清单
- [ ] 已理解名字隐藏与重载的区别
- [ ] 已会用 using 解锁基类同名函数
- [ ] 已知道同名成员变量也会隐藏
- [ ] 已能避免继承中的同名函数陷阱
