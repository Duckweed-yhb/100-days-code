# Day 32 · 虚析构函数

## 今日目标
- 理解为什么基类析构函数要写成 virtual
- 识别"基类指针 delete 派生类对象"的坑
- 记住规则：有虚函数的基类析构必须是 virtual

## 学习内容

### 问题场景

```cpp
class Animal {
public:
    ~Animal() { std::cout << "~Animal\n"; }     // 不是 virtual
};
class Dog : public Animal {
public:
    ~Dog() { std::cout << "~Dog\n"; }
};

int main() {
    Animal* p = new Dog;
    delete p;     // 只输出 ~Animal！Dog 的析构没被调用
}
```

通过基类指针 delete 派生类对象时，如果基类析构不是 virtual，**只会调基类析构**，派生类资源泄漏。

### 修复：把基类析构写成 virtual

```cpp
class Animal {
public:
    virtual ~Animal() { std::cout << "~Animal\n"; }
};
// delete p 时：~Dog -> ~Animal，正确
```

规则：**只要一个类打算当基类、且会被多态使用（通过基类指针 delete），它的析构函数就必须是 virtual**。

### 反面：不该 virtual 的情况

如果类不是用来当基类的（比如标准库里的 `std::string`），加 virtual 反而会引入虚表指针开销。经验：类里只要有一个虚函数，析构就该是 virtual。

### = default 写法

现代 C++ 常这么写：

```cpp
class Animal {
public:
    virtual ~Animal() = default;
    // ... 其他虚函数
};
```

### 与三法则的关系

一旦析构函数是 virtual，派生类的析构即使不显式写 virtual，也会自动是虚函数（只要签名一致）。

## 核心重点
- 通过基类指针 delete 派生类对象时，基类析构必须 virtual
- 否则只调基类析构，派生类资源泄漏
- 有虚函数的类，析构就该是 virtual
- 不是用来当基类的类，不需要 virtual 析构

## 练习/任务
1. 复现上面的"只调基类析构"bug
2. 把 Animal 析构改成 virtual，观察完整析构链
3. 在 Dog 构造里 new 一块内存，析构里 delete，验证 bug 修复前后是否泄漏
4. （选做）写一个基类没有 virtual 析构，但派生类有资源，用 valgrind（或任务管理器）观察泄漏

## 今日刷题

- **LeetCode 102. 二叉树的层序遍历**：用队列 BFS，每轮把当前层所有节点取出、记录值、把下一层入队。
- **LeetCode 199. 二叉树的右视图**：层序遍历，每层只取最右节点。
- **LeetCode 113. 路径总和 II**：在 Day30 路径总和基础上，记录所有符合条件的路径（vector<int> 临时路径回溯）。

## 打卡清单
- [ ] 已复现并修复虚析构 bug
- [ ] 已记住"基类析构必须 virtual"规则
- [ ] 已完成至少一道树刷题
- [ ] 已理解为什么有虚函数就要 virtual 析构
