# Day 49 · 函数对象与lambda

## 今日目标
- 理解函数对象（仿函数）
- 学会写 lambda 表达式
- 在 STL 算法中使用 lambda

## 学习内容

### 函数对象

重载了 `operator()` 的类对象，可以像函数一样调用：

```cpp
struct AddX {
    int x;
    AddX(int x) : x(x) {}
    int operator()(int y) const { return x + y; }
};

AddX add10(10);
std::cout << add10(5);    // 15
```

### lambda 表达式

C++11 起可以就地写匿名函数：

```cpp
auto f = [](int a, int b) { return a + b; };
std::cout << f(3, 4);    // 7
```

语法：

```
[捕获列表](参数列表) -> 返回类型 { 函数体 }
```

返回类型通常自动推导，不用写。

### 捕获列表

lambda 可以"捕获"所在作用域的变量：

```cpp
int offset = 10;
auto addOffset = [offset](int x) { return x + offset; };
std::cout << addOffset(5);   // 15
```

| 捕获写法 | 含义 |
|---|---|
| `[]` | 不捕获 |
| `[x]` | 按值捕获 x |
| `[&x]` | 按引用捕获 x |
| `[=]` | 按值捕获所有用到的变量 |
| `[&]` | 按引用捕获所有用到的变量 |
| `[=, &x]` | 默认按值，x 按引用 |

### 在 STL 算法中用 lambda

```cpp
std::vector<int> v{3, 1, 4, 1, 5, 9, 2, 6};

// 降序
std::sort(v.begin(), v.end(), [](int a, int b) { return a > b; });

// 统计偶数个数
int even = std::count_if(v.begin(), v.end(), [](int x) { return x % 2 == 0; });

// 打印每个元素
std::for_each(v.begin(), v.end(), [](int x) { std::cout << x << ' '; });
```

### 可变 lambda

按值捕获的变量默认在 lambda 内是 const，要修改需加 `mutable`：

```cpp
int count = 0;
auto counter = [count]() mutable { return ++count; };
```

工程里更常用引用捕获：`[&count]`。

### lambda 与回调

lambda 是现代 C++ 最常用的特性之一：排序比较、事件回调、STL 算法参数、线程入口都用它。

## 核心重点
- lambda：`[捕获](参数){ 函数体 }`
- 捕获列表决定能用哪些外部变量
- `[=]` 按值，`[&]` 按引用
- STL 算法传 lambda 最方便
- 函数对象是 lambda 的"老祖宗"

## 练习/任务
1. 用 lambda 让 sort 按绝对值排序
2. 用 count_if 统计 vector 中大于平均值的元素个数
3. 写一个 lambda 捕获局部变量，验证按值与按引用的区别
4. （选做）用 for_each + lambda 打印 vector 中所有偶数

## 今日刷题

- **LeetCode 973. 最接近原点的 K 个点**：用 sort 或 priority_queue，比较函数是距离平方和。
- **LeetCode 692. 前 K 个高频单词**：统计频率后用 sort，lambda 先按频率降序再按字典序升序。
- **LeetCode 347. 前 K 个高频元素**：频率排序，取前 k。

## 打卡清单
- [ ] 已会写 lambda
- [ ] 已理解捕获列表
- [ ] 已会在 sort/count_if 里用 lambda
- [ ] 已完成至少一道自定义比较题
