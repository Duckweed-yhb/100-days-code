# Day 59 · lambda深入

## 今日目标
- 理解 lambda 与捕获的本质
- 掌握 mutable、延时调用
- 学会用 lambda 做回调

## 学习内容

### lambda 的本质

编译器会把 lambda 翻译成一个**匿名类**，重载 `operator()`：

```cpp
auto f = [x](int y) { return x + y; };
// 等价于：
struct F {
    int x;
    int operator()(int y) const { return x + y; }
};
F f{x};
```

理解这点，捕获列表就好懂了：捕获的变量变成匿名类的成员。

### 按值 vs 按引用

```cpp
int count = 0;

auto byVal = [count]() mutable { return ++count; };   // 副本，不影响外面
auto byRef = [&count]() { return ++count; };          // 改外面

byVal();
std::cout << count;     // 0
byRef();
std::cout << count;     // 1
```

### 延时调用

lambda 可以存起来以后再用：

```cpp
std::vector<std::function<void()>> tasks;
tasks.push_back([](){ std::cout << "A\n"; });
tasks.push_back([](){ std::cout << "B\n"; });

for (auto& t : tasks) t();    // 稍后执行
```

> `std::function` 在 `<functional>` 里，是"能装任意可调用对象"的容器。

### lambda 作回调

```cpp
void onButtonClick(std::function<void()> cb) {
    cb();
}

onButtonClick([](){ std::cout << "按钮被点了\n"; });
```

### 泛型 lambda（C++14）

```cpp
auto add = [](auto a, auto b) { return a + b; };
add(1, 2);
add(1.5, 2.5);
```

## 核心重点
- lambda 本质是带 operator() 的匿名类对象
- 按值捕获是副本，按引用捕获改外面
- mutable 允许改按值捕获的副本
- std::function 能存 lambda
- lambda 是回调的首选写法

## 练习/任务
1. 写一个 lambda 按值捕获 count，用 mutable 自增，验证不影响外面
2. 写一个 lambda 按引用捕获 count，自增后外面可见
3. 用 std::function 存三个不同 lambda，循环调用
4. （选做）写一个简单的事件队列：push lambda 进去，稍后统一执行

## 今日刷题

- **LeetCode 70. 爬楼梯**：DP，`dp[i] = dp[i-1] + dp[i-2]`。
- **LeetCode 118. 杨辉三角**：DP，每行首尾为 1，中间 `a[i][j] = a[i-1][j-1] + a[i-1][j]`。
- **LeetCode 198. 打家劫舍**：DP。

## 打卡清单
- [ ] 已理解 lambda 是匿名类实例
- [ ] 已分清按值与按引用捕获
- [ ] 已会用 std::function
- [ ] 已完成至少一道 DP 题
