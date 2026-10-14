# Day 56 · auto与decltype

## 今日目标
- 熟练使用 auto 类型推导
- 学会用 decltype 获取表达式类型
- 了解尾置返回类型

## 学习内容

### auto 基本用法

```cpp
auto i = 42;              // int
auto d = 3.14;            // double
auto s = "hello";         // const char*
auto v = std::vector<int>{1,2,3};   // vector<int>
```

auto 让编译器推导类型，少写冗长的类型名。

### auto 与引用/指针

```cpp
int x = 10;
auto a = x;           // int（副本）
auto& ar = x;         // int&
const auto& cr = x;   // const int&
```

遍历容器时推荐 `const auto&`：

```cpp
for (const auto& item : container) { ... }
```

避免不必要的拷贝。

### auto 会"剥掉"引用

```cpp
int& r = x;
auto a = r;       // a 是 int，不是 int&
auto& ar = r;     // ar 才是 int&
```

### decltype

`decltype(expr)` 得到表达式的类型：

```cpp
int x = 10;
decltype(x) y = 20;     // y 是 int
decltype((x)) z = x;    // z 是 int&（注意括号）
```

### 尾置返回类型

```cpp
template <typename T, typename U>
auto add(T a, U b) -> decltype(a + b) {
    return a + b;
}
```

C++14 起直接 `auto` 返回就行：

```cpp
template <typename T, typename U>
auto add(T a, U b) { return a + b; }
```

### 什么时候别用 auto

- 类型不明确会影响可读性时；
- 需要隐式类型转换时（auto 会保留原始类型）。

## 核心重点
- auto 让编译器推导类型，简化代码
- 遍历容器用 `const auto&` 避免拷贝
- auto 会剥掉引用，要引用写 `auto&`
- decltype 获取表达式类型
- 复杂返回值用尾置或 C++14 auto 返回

## 练习/任务
1. 把你之前写的代码里冗长类型都换成 auto
2. 用 `const auto&` 遍历 vector<vector<int>>
3. 写函数模板 add，返回值类型自动推导
4. （选做）用 decltype 推导 `1 + 2.0` 的类型

## 今日刷题

- **LeetCode 142. 环形链表 II**：快慢指针找环起点。
- **LeetCode 160. 相交链表**：双指针走对方路径。
- **LeetCode 92. 反转链表 II**：局部反转区间。

## 打卡清单
- [ ] 已熟练使用 auto
- [ ] 已会用 const auto& 遍历
- [ ] 已了解 decltype
- [ ] 已完成至少一道链表题
