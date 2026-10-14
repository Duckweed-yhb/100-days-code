# Day 60 · 初始化列表与聚合初始化

## 今日目标
- 熟练使用花括号初始化 `{...}`
- 理解聚合初始化
- 了解 std::initializer_list

## 学习内容

### 花括号初始化

C++11 统一了初始化语法：

```cpp
int x{42};
double d{3.14};
std::vector<int> v{1, 2, 3};
std::pair p{1, std::string{"a"}};
```

优点：

- 统一写法；
- 不允许窄化转换（`int x{3.14}` 编译错）；
- 不会"最令人头疼的解析"（most vexing parse）。

### 聚合初始化

类如果所有成员都是 public、没有自定义构造函数，就能直接用花括号：

```cpp
struct Point {
    int x;
    int y;
};
Point p{1, 2};
```

### std::initializer_list

构造函数接收 `std::initializer_list<T>`，就能让对象支持花括号列表初始化：

```cpp
class MyVector {
    std::vector<int> data_;
public:
    MyVector(std::initializer_list<int> il) : data_(il) {}
};

MyVector mv{1, 2, 3, 4};
```

`std::vector`、`std::map` 都有这个构造函数，所以才能 `vector<int>{1,2,3}`。

### initializer_list 遍历

```cpp
int sum = 0;
for (int x : il) sum += x;
```

### 与函数模板配合

```cpp
template <typename T>
void printAll(std::initializer_list<T> items) {
    for (const auto& x : items) std::cout << x << ' ';
}
printAll({1, 2, 3});
```

## 核心重点
- 花括号初始化统一且防窄化
- 聚合类型直接 `T{...}` 初始化
- initializer_list 让类支持花括号传参
- vector/map 都靠这个实现列表初始化

## 练习/任务
1. 用花括号初始化 int、double、vector、map
2. 写一个类，构造函数接收 initializer_list<int>
3. 写函数 printAll 接收 initializer_list<int>
4. （选做）故意写 `int x{3.14}`，观察编译错误

## 今日刷题

- **LeetCode 120. 三角形最小路径和**：DP，从下往上。
- **LeetCode 64. 最小路径和**：DP 网格。
- **LeetCode 5. 最长回文子串**：中心扩展或 DP。

## 打卡清单
- [ ] 已习惯用花括号初始化
- [ ] 已理解聚合初始化
- [ ] 已会用 initializer_list
- [ ] 已完成至少一道 DP 题
