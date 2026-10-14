# Day 38 · 函数模板基础

## 今日目标
- 理解泛型编程的思想：写一次，适配多类型
- 学会定义与调用函数模板
- 区分模板与重载

## 学习内容

### 为什么需要模板

为 int 写一个 `max`，为 double 又写一个，为 string 再写一个？代码完全一样，只是类型不同。函数模板让你写一次，编译器自动生成对应版本。

### 定义函数模板

```cpp
template <typename T>
T myMax(T a, T b) {
    return a > b ? a : b;
}

int main() {
    std::cout << myMax(3, 5) << '\n';          // T = int
    std::cout << myMax(1.5, 2.5) << '\n';     // T = double
    std::cout << myMax(std::string("apple"), std::string("banana")) << '\n';
}
```

- `template <typename T>` 声明这是模板，`T` 是类型参数；
- 编译器根据实参**推导** T 的类型，自动生成 `myMax(int,int)`、`myMax(double,double)` 等版本。

### 显式指定类型

```cpp
myMax<double>(3, 5);    // 强制 T = double
```

当编译器推不出来时（比如参数类型不一致），就要显式写。

### 模板与重载的区别

- 重载：手写多个版本，编译器选最合适的；
- 模板：写一份通用代码，编译器自动生成多份。

模板是"更聪明的重载"。

### 多类型参数

```cpp
template <typename T, typename U>
auto add(T a, U b) -> decltype(a + b) {
    return a + b;
}
```

C++14 起甚至可以直接 `auto` 返回：

```cpp
template <typename T, typename U>
auto add(T a, U b) { return a + b; }
```

### 模板本质

模板不是函数，而是"生成函数的蓝图"。编译器在编译时根据具体类型实例化出真正的函数。这也是为什么模板代码通常都写在头文件里（Day69 讲）。

## 核心重点
- 函数模板 = 写一份代码，适配多类型
- `template <typename T>` 开头
- 编译器根据实参推导 T
- 模板在编译期实例化
- 多类型参数用 `template <typename T, typename U>`

## 练习/任务
1. 写函数模板 `mySwap(T& a, T& b)` 交换两个变量
2. 写函数模板 `printArray(const T arr[], int n)` 打印任意类型数组
3. 写函数模板 `myMax3(T a, T b, T c)` 返回三者最大
4. （选做）写一个模板函数，参数是 `std::vector<T>`，返回最大值

## 今日刷题

- **LeetCode 704. 二分查找**：标准模板，`[l, r]` 闭区间，`while (l <= r)`。
- **LeetCode 278. 第一个错误的版本**：二分，找第一个 isBadVersion(v) 为 true 的位置。
- **LeetCode 35. 搜索插入位置**：二分，找不到时返回应插入位置。

## 打卡清单
- [ ] 已会定义函数模板
- [ ] 已会让编译器推导类型
- [ ] 已理解模板是编译期生成代码
- [ ] 已完成二分查找刷题
