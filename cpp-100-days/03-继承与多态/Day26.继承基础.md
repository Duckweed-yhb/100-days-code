# Day 26 · 继承基础

## 今日目标
- 理解继承："是一个"的关系
- 学会用 `class 派生类 : 继承方式 基类`
- 区分 public / protected / private 继承

## 学习内容

### 为什么需要继承

已经写了 `Animal` 类，现在要写 `Dog`、`Cat`。它们都有 name()、eat()，但又各自有 bark()、meow()。如果重写一遍，代码大量重复。

**继承**让派生类自动获得基类的成员，再加上自己的扩展：

```cpp
class Animal {
public:
    std::string name;
    void eat() { std::cout << name << " 在吃饭\n"; }
};

class Dog : public Animal {     // Dog "是一个" Animal
public:
    void bark() { std::cout << name << " 汪汪汪\n"; }
};

int main() {
    Dog d;
    d.name = "旺财";
    d.eat();     // 继承来的
    d.bark();    // 自己的
}
```

### 继承方式

`class Dog : public Animal` 中的 `public` 是继承方式：

| 继承方式 | 基类 public 成员在派生类中变 | 基类 protected 成员在派生类中变 |
|---|---|---|
| public 继承 | public | protected |
| protected 继承 | protected | protected |
| private 继承 | private | private |

90% 的场景用 **public 继承**，表示"is-a"（是一个）。其他两种很少用。

### 基类与派生类的构造析构顺序

```cpp
class Base {
public:
    Base()  { std::cout << "Base()\n"; }
    ~Base() { std::cout << "~Base()\n"; }
};
class Derived : public Base {
public:
    Derived()  { std::cout << "Derived()\n"; }
    ~Derived() { std::cout << "~Derived()\n"; }
};
// 创建 Derived：Base() -> Derived()
// 销毁：~Derived() -> ~Base()
```

永远是先构造基类，再构造派生类；析构反过来。

### 派生类不能访问基类 private

基类的 private 成员派生类也访问不到。如果想让派生类能访问，但对外仍隐藏，就用 `protected`：

```cpp
class Animal {
protected:
    std::string name;      // 派生类能访问，外部不能
};
```

### 派生类对象里有什么

一个 Dog 对象里其实包含两部分：一份 Animal 子对象 + 一份 Dog 自己新增的成员。内存上先放基类子对象，再放派生类成员。

## 核心重点
- 继承表示"is-a"：Dog is an Animal
- 90% 用 public 继承
- 构造先基类后派生，析构相反
- protected 成员派生类可见，外部不可见
- 派生类对象 = 基类子对象 + 派生类新成员

## 练习/任务
1. 写 `Shape` 基类（含 name），派生出 `Circle` 和 `Rectangle`
2. 写 `Person` 基类（姓名、年龄），派生出 `Student`（学号、分数）和 `Teacher`（工资）
3. 在派生类构造函数里加打印，观察构造顺序
4. （选做）把基类成员改成 private，尝试在派生类里访问，观察编译错误

## 今日刷题

- **LeetCode 703. 数据流中的第 K 大元素**：用大小为 k 的最小堆。
- **LeetCode 933. 最近的请求次数**：队列，每次把超过 3000 毫秒前的弹出。
- **LeetCode 104. 二叉树的最大深度**：递归 `1 + max(left, right)`。

## 打卡清单
- [ ] 已会用 public 继承
- [ ] 已能预测构造析构顺序
- [ ] 已分清 private 与 protected
- [ ] 已理解 is-a 关系
