# Day 07 · 函数基础与参数传递

## 今日目标
- 学会定义、声明、调用函数
- 理解值传递与地址传递的区别
- 了解函数声明（原型）与头文件的关系

## 学习内容

### 定义一个函数

```cpp
#include <iostream>

// 返回值类型  函数名(参数列表) { 函数体 }
int add(int a, int b) {
    return a + b;
}

int main() {
    std::cout << add(3, 4) << '\n';   // 输出 7
}
```

要点：

- 返回类型写在函数名前；不返回东西就写 `void`；
- 参数列表里每个参数都要写类型：`(int a, int b)`，不能写成 `(int a, b)`；
- `return` 把结果送回调用处。

### 函数声明（原型）

C++ 要求"先声明后使用"。如果函数定义写在 main 后面，要在前面先声明：

```cpp
#include <iostream>

int add(int a, int b);     // 声明（原型），参数名可省略

int main() {
    std::cout << add(1, 2) << '\n';
}

int add(int a, int b) {    // 真正定义
    return a + b;
}
```

工程里通常把声明放进 `.h` 头文件，定义放进 `.cpp`，Day68 会细讲。

### 值传递

默认情况下，C++ 函数参数是**值传递**：把实参复制一份给形参，函数内改的是副本，不影响外面。

```cpp
void increase(int x) {
    x = x + 10;          // 改的是副本
}

int main() {
    int n = 5;
    increase(n);
    std::cout << n;      // 仍然是 5
}
```

### 指针传递

想在函数里改外面的变量，就传地址：

```cpp
void increase(int* x) {   // 参数是指针
    *x = *x + 10;        // 通过解引用改原变量
}

int main() {
    int n = 5;
    increase(&n);        // 传 n 的地址
    std::cout << n;      // 15
}
```

### 传递数组

数组传参会退化为指针，长度信息丢失：

```cpp
int sum(const int arr[], int n) {   // arr 其实是 const int*
    int s = 0;
    for (int i = 0; i < n; ++i) s += arr[i];
    return s;
}
```

所以数组作为参数时，**必须另外传长度**。

### 函数可以重载（先看一眼）

同名函数只要参数列表不同就能共存：

```cpp
int  add(int a, int b)         { return a + b; }
double add(double a, double b) { return a + b; }
```

Day08 会专门讲。

## 核心重点
- 函数 = 返回类型 + 名字 + 参数列表 + 函数体
- 先声明后使用；声明可以放在头文件里
- 默认是值传递，改形参不影响实参
- 想在函数里改外面的变量：传指针（明天学引用，更优雅）
- 数组传参会退化为指针，必须额外传长度

## 练习/任务
1. 写函数 `bool isPrime(int n)` 判断素数，main 中调用
2. 写函数 `int gcd(int a, int b)` 求最大公约数（辗转相除法）
3. 写函数 `void swap(int* a, int* b)` 交换两个变量
4. （选做）写函数求数组最大值，在 main 中传入数组并打印

## 打卡清单
- [ ] 已会定义与声明函数
- [ ] 已理解值传递不改实参
- [ ] 已会用指针在函数内修改实参
- [ ] 已知道数组传参要带长度
