# Day 33 · 多继承与接口隔离

## 今日目标
- 了解多继承的写法与"菱形继承"问题
- 知道虚继承 `virtual` 的作用
- 学会用接口隔离替代臃肿基类

## 学习内容

### 多继承

C++ 允许一个类同时继承多个基类：

```cpp
class Runner { public: void run() {} };
class Swimmer { public: void swim() {} };

class Duck : public Runner, public Swimmer {};

Duck d;
d.run();
d.swim();
```

多继承用得好可以灵活组合能力（"能跑" + "能游"），用得乱就是灾难。

### 菱形继承问题

```
   Animal
   /    \
 Dog    Cat
   \    /
   Labrador   ??? 不太合理，举另一个例子：
```

更典型的菱形：

```cpp
class Person { public: std::string name; };
class Student : public Person {};
class Teacher : public Person {};
class Assistant : public Student, public Teacher {};
// Assistant 里有两份 Person 子对象！
```

访问 `Assistant.name` 会编译错误：到底是 Student 的 Person 还是 Teacher 的？

### 虚继承解决菱形

让 Student 和 Teacher 虚继承 Person：

```cpp
class Person { public: std::string name; };
class Student : virtual public Person {};
class Teacher : virtual public Person {};
class Assistant : public Student, public Teacher {};

Assistant a;
a.name = "张三";     // 现在只有一份 Person 子对象
```

`virtual public` 让最远派生类负责构造共享的虚基类子对象。

### 接口隔离原则

更常见的做法是：不要一个臃肿的大基类，而是拆成多个小接口，需要哪个继承哪个：

```cpp
class Printable { public: virtual std::string toString() const = 0; };
class Serializable { public: virtual std::vector<char> serialize() const = 0; };

class MyData : public Printable, public Serializable {
    // 只实现自己需要的接口
};
```

这比"一个巨型 Shape 基类塞所有方法"灵活得多。

### 现代 C++ 的倾向

C++ 社区现在的主流观点：

- 优先用单继承 + 接口（纯虚类）；
- 多继承只用于"混合小接口"，不要用于实现继承；
- 菱形继承尽量通过设计绕开。

## 核心重点
- C++ 支持多继承：`class D : public A, public B {}`
- 菱形继承会产生多份基类子对象
- `virtual public` 虚继承让多份合并成一份
- 优先接口隔离：多个小接口比一个大基类好
- 多继承慎用，主要用来组合纯虚接口

## 练习/任务
1. 写 `Flyable` 和 `Swimmable` 两个纯虚接口，让 `Duck` 同时实现
2. 复现菱形继承的二义性错误
3. 用 virtual public 修复菱形继承
4. （选做）把 Day31 的 Logger 拆成两个接口：Writer 和 Formatter

## 今日刷题

- **LeetCode 236. 二叉树的最近公共祖先**：递归，如果 root 是 p 或 q 就返回 root；左右分别找，一边返回 NULL 就取另一边，两边都有就返回当前 root。
- **LeetCode 198. 打家劫舍**：DP，`dp[i] = max(dp[i-1], dp[i-2] + nums[i])`。
- **LeetCode 139. 单词拆分**：DP，`dp[i]` 表示前 i 个字符能否被拆分，遍历词典看 `dp[j] && wordDict.count(s[j:i])`。

## 打卡清单
- [ ] 已会写多继承
- [ ] 已理解菱形继承问题
- [ ] 已会用 virtual public 解决菱形
- [ ] 已完成至少一道 DP/树刷题
