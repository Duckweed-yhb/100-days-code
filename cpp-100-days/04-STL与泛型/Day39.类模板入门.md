# Day 39 · 类模板入门

## 今日目标
- 学会定义类模板
- 理解 STL 容器为什么都是模板
- 写一个简单的泛型"栈"类

## 学习内容

### 为什么需要类模板

前面写的 `IntVector` 只能装 int。想装 double、string 怎么办？类模板让"容器"与"元素类型"解耦。

### 定义类模板

```cpp
template <typename T>
class Stack {
    T* data_;
    int top_;
    int capacity_;
public:
    explicit Stack(int cap = 100) : top_(0), capacity_(cap) {
        data_ = new T[cap];
    }
    ~Stack() { delete[] data_; }

    void push(const T& x) { data_[top_++] = x; }
    T pop() { return data_[--top_]; }
    bool empty() const { return top_ == 0; }
};
```

### 使用类模板

使用时必须显式指定类型：

```cpp
Stack<int> si;
si.push(1);
si.push(2);

Stack<std::string> ss;
ss.push("hello");
```

### 类模板的成员函数

类模板里的成员函数，只有被调用时才会被实例化。这意味着你可以写一个 Stack 类，即使 `T = File` 没用到 `pop()`，也不会因为 File 不支持拷贝而报错。

### 默认类型参数

```cpp
template <typename T, typename Container = std::deque<T>>
class stack {
    Container c_;
public:
    void push(const T& x) { c_.push_back(x); }
    // ...
};
```

STL 里 `std::stack<T>` 第二个参数有默认值，所以日常只写 `stack<int>`。

### 模板与 STL

`std::vector<T>`、`std::map<K,V>` 全都是类模板。今天理解了模板，STL 就不再是黑魔法。

## 核心重点
- 类模板：`template <typename T> class Stack { ... };`
- 使用时显式指定类型：`Stack<int>`
- 成员函数用到时才实例化
- 模板参数可以有默认值
- STL 容器本质都是类模板

## 练习/任务
1. 完成上面的泛型 Stack，测试 push/pop/empty
2. 写类模板 `MyPair<T1, T2>`，存两个不同类型的值
3. 写类模板 `Box<T>`，含一个 T 成员和 get/set
4. （选做）给 Stack 加 size() 和 peek() 方法

## 今日刷题

- **LeetCode 20. 有效的括号**：栈，左括号入栈，右括号看栈顶是否匹配。
- **LeetCode 155. 最小栈**：两个栈，一个存数据，一个同步存当前最小值。
- **LeetCode 232. 用栈实现队列**：两个栈，push 时进栈 A，pop 时若栈 B 空就把 A 全倒到 B。

## 打卡清单
- [ ] 已会定义类模板
- [ ] 已写过泛型 Stack
- [ ] 已理解 STL 容器是类模板
- [ ] 已完成栈相关刷题
