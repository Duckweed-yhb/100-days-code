# Day 10 · const与指针

## 今日目标
- 分清 const 修饰指针的几种写法
- 学会用 const 保护数据不被误改
- 理解 const 与引用配合的常见模式

## 学习内容

### const 修饰变量

```cpp
const int a = 10;     // a 是只读的
// a = 20;            // 编译错误
```

### const 与指针的两种组合

这是 C++ 新手最容易绕晕的地方，关键看 `const` 修饰的是谁。

```cpp
int x = 10, y = 20;

const int* p1 = &x;    // 指向 const int 的指针：不能通过 p1 改 x
// *p1 = 30;           // 错
p1 = &y;               // 对：p1 可以改指向别人

int* const p2 = &x;    // const 指针：指针本身不能改指向
p2 = &y;               // 错
*p2 = 30;              // 对：可以通过 p2 改 x

const int* const p3 = &x;  // 都不能改：不能改指向，也不能改值
```

记忆口诀：**从右往左读类型**。

- `const int* p1`：p1 是指针，指向 const int；
- `int* const p2`：p2 是 const 指针，指向 int。

### const 与引用

`const T&` 是工程里最常见的形式：

```cpp
void f(const std::string& s);   // 不拷贝、不修改
```

它比 `T` 传值省内存，比 `T&` 更安全。

### 指向非 const 的指针/引用可以隐式转 const

```cpp
int x = 10;
int* p = &x;
const int* cp = p;       // 可以：int* -> const int*
// int* q = cp;          // 错：反过来不行
```

也就是说，你可以"承诺不改"，但不能把别人 const 的数据偷偷改了。

### const 成员函数（先认识）

类里不修改成员变量的方法可以声明为 const：

```cpp
class Point {
    int x_, y_;
public:
    int getX() const { return x_; }   // 承诺不改成员
};
```

Day14 学类时会再见到。

### 实战：用 const 保护数组参数

```cpp
double average(const double scores[], int n) {
    double s = 0;
    for (int i = 0; i < n; ++i) s += scores[i];
    return n > 0 ? s / n : 0.0;
}
```

`const double scores[]` 告诉调用者：我只读你的数组，不会改。

## 核心重点
- `const T* p`：不能通过 p 改值，但 p 可以改指向
- `T* const p`：p 不能改指向，但可以通过 p 改值
- `const T* const p`：都不能改
- `const T&` 是传大对象的最佳实践
- 能用 const 就加 const，是防止 bug 的第一道防线

## 练习/任务
1. 写函数 `double average(const double arr[], int n)` 求平均值
2. 写函数 `void printArray(const int arr[], int n)` 打印数组
3. 故意在函数里写 `arr[0] = 999;`，观察编译错误
4. （选做）自己画一张表，列出 const int*、int* const、const int* const 三种指针"能不能改值、能不能改指向"

## 打卡清单
- [ ] 已分清 const 修饰指针的两种位置
- [ ] 已养成给只读参数加 const 的习惯
- [ ] 已理解 const T& 的含义
- [ ] 已知道 int* 可隐式转 const int*
