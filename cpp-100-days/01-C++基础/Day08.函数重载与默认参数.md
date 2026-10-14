# Day 08 · 函数重载与默认参数

## 今日目标
- 理解函数重载的规则与限制
- 学会使用默认参数
- 了解内联函数 inline

## 学习内容

### 什么是函数重载

同一个作用域里，函数名相同但**参数列表不同**（类型或个数不同），就算重载。编译器根据调用时的实参自动选最合适的版本。

```cpp
#include <iostream>

int  add(int a, int b)              { return a + b; }
double add(double a, double b)      { return a + b; }
int  add(int a, int b, int c)      { return a + b + c; }

int main() {
    std::cout << add(1, 2)        << '\n';   // 调 int 版本
    std::cout << add(1.5, 2.5)    << '\n';   // 调 double 版本
    std::cout << add(1, 2, 3)     << '\n';   // 调三参版本
}
```

### 重载的限制

- **仅返回值类型不同**不算重载：`int f()` 和 `void f()` 不能同时存在；
- 参数类型必须真的不同：`int` 和 `int` 不算；
- `typedef` 起的别名与原类型算同一个类型。

### 默认参数

调用时可以省略某些参数，函数定义时给默认值即可：

```cpp
// 第二个参数默认是 10
int power(int base, int exp = 2) {
    int r = 1;
    for (int i = 0; i < exp; ++i) r *= base;
    return r;
}

int main() {
    power(3);      // 等价于 power(3, 2)，结果 9
    power(3, 3);   // 27
}
```

规则：

- 默认参数必须从右往左连续给，不能"跳着给"：`void f(int a, int b = 1, int c = 2)` 合法；`void f(int a = 1, int b)` 不合法；
- 声明在头文件时，默认值写在声明里，定义里不要再写一遍。

### 函数重载与默认参数的配合坑

默认参数有时会让重载产生歧义：

```cpp
void f(int x);
void f(int x, int y = 0);

f(10);    // 编译报错：两个函数都能匹配，编译器不知道选谁
```

这种情况要靠人工合并重载版本来避免。

### 内联函数 inline

函数调用有"压栈跳转"的开销。如果函数很小、又被频繁调用，可以加 `inline` 建议编译器把函数体"内联展开"到调用处：

```cpp
inline int sqr(int x) { return x * x; }
```

`inline` 只是对编译器的建议，不是强制。现代编译器会自行决定是否内联。

### 函数模板（先看一眼）

重载多个 `add` 很啰嗦，C++ 有更通用的写法——函数模板：

```cpp
template <typename T>
T add(T a, T b) { return a + b; }
```

Day38 会专门讲，今天先有个印象。

## 核心重点
- 重载 = 同名函数 + 不同参数列表；只改返回值不算
- 默认参数从右往左连续给，声明处写一次
- 重载 + 默认参数可能产生二义性，要小心
- `inline` 建议编译器展开小函数
- 模板是重载的"升级版"，后面会学

## 练习/任务
1. 写一组重载的 `print`：能打印 int、double、const char*
2. 写函数 `int boxVolume(int w, int h = 1, int d = 1)`，计算长方体体积
3. 写内联函数 `int max2(int a, int b)` 返回较大者
4. （选做）故意写一个仅返回值不同的重载，观察编译报错

## 打卡清单
- [ ] 已理解函数重载的判定规则
- [ ] 已会写默认参数
- [ ] 已知道默认参数必须从右往左
- [ ] 已了解 inline 的作用
