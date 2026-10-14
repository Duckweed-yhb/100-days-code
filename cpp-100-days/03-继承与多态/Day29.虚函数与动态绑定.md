# Day 29 · 虚函数与动态绑定

## 今日目标
- 理解为什么需要虚函数
- 学会用 `virtual` 实现运行时多态
- 区分静态绑定与动态绑定

## 学习内容

### 问题：基类指针调到哪个函数？

```cpp
class Animal {
public:
    void speak() { std::cout << "??\n"; }
};
class Dog : public Animal {
public:
    void speak() { std::cout << "汪汪\n"; }
};

void say(Animal* a) { a->speak(); }

int main() {
    Dog d;
    say(&d);     // 期望输出"汪汪"，实际输出"??"
}
```

不加 `virtual` 时，编译器看 `a` 的类型是 `Animal*`，就调 `Animal::speak()`，**静态绑定**。

### virtual 解决问题

```cpp
class Animal {
public:
    virtual void speak() { std::cout << "??\n"; }    // 加 virtual
};
class Dog : public Animal {
public:
    void speak() override { std::cout << "汪汪\n"; } // override 显式标记
};

void say(Animal* a) { a->speak(); }   // 现在会调到 Dog::speak
```

加了 `virtual`，编译器根据**对象的实际类型**决定调哪个函数，这叫**动态绑定**。

### override 关键字

C++11 起，派生类重写虚函数时加 `override`，编译器帮你检查签名是否真的和基类一致：

```cpp
class Dog : public Animal {
public:
    void speak() override;    // 推荐：签名写错会编译报错
};
```

如果漏写 override，或者签名写错了（比如参数不一样），就变成 Day28 的"隐藏"而不是"重写"。

### 静态绑定 vs 动态绑定

| 绑定方式 | 何时决定 | 触发条件 |
|---|---|---|
| 静态绑定 | 编译期 | 普通函数、非虚函数 |
| 动态绑定 | 运行期 | 虚函数 + 基类指针/引用 |

### 虚函数的实现简介

编译器为有虚函数的类生成一张**虚函数表（vtable）**，每个对象里放一个虚表指针（vptr）。调用虚函数时，通过 vptr 找到 vtable，再查到实际函数地址。这是虚函数一点点性能开销的来源。

## 核心重点
- 不加 virtual：基类指针调基类版本（静态绑定）
- 加 virtual：根据对象实际类型调（动态绑定）
- 派生类重写时加 `override` 防止写错签名
- 多态 = 同一接口，不同实现
- 虚函数通过 vtable 实现，有少量运行开销

## 练习/任务
1. 把 Day26 的 Animal speak() 改成 virtual，Dog/Cat 各自重写
2. 用 `Animal*` 数组同时放 Dog 和 Cat 指针，循环调 speak()，观察多态
3. 故意把派生类 speak 签名写错（比如加个参数），观察 override 如何报错
4. （选做）去掉 override，故意签名写错，观察它悄悄变成"隐藏"而非重写

## 今日刷题

- **LeetCode 102. 二叉树的层序遍历**：BFS 队列。
- **LeetCode 199. 二叉树的右视图**：层序每层取最右。
- **LeetCode 107. 二叉树的层序遍历 II**：从底向上层序。

## 打卡清单
- [ ] 已理解静态绑定与动态绑定
- [ ] 已会用 virtual 实现多态
- [ ] 已会用 override 标记重写
- [ ] 已能写出多态数组的例子
