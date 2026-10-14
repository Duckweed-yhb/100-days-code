# Day 20 · static静态成员

## 今日目标
- 理解静态成员变量属于类而不属于对象
- 学会用静态成员函数
- 了解 static 在函数内局部变量上的用法

## 学习内容

### 静态成员变量

普通成员变量每个对象一份拷贝；**静态成员变量整个类只有一份**，被所有对象共享：

```cpp
class Student {
public:
    std::string name;
    static int totalCount;          // 声明：不属于任何对象

    Student(const std::string& n) : name(n) { ++totalCount; }
    ~Student() { --totalCount; }
};

int Student::totalCount = 0;        // 定义：必须在类外分配内存

int main() {
    Student a("A"), b("B");
    std::cout << Student::totalCount;   // 2
}
```

注意：

- 类内只是**声明**，必须在类外再**定义**一次（C++17 起可以用 `inline static` 跳过这步，但今天先按传统写法）；
- 访问方式：`类名::静态成员` 或 `对象.静态成员`。

### 静态成员函数

静态成员函数没有 `this` 指针，只能访问静态成员：

```cpp
class Account {
    static double totalMoney_;
public:
    static double totalMoney() { return totalMoney_; }
};
```

调用：`Account::totalMoney()`。

### 静态成员的典型用途

- 统计类被实例化了多少次；
- 全类共享的常量/计数器；
- 工具函数（不需要访问对象状态）。

### 函数内的 static 局部变量

```cpp
void counter() {
    static int n = 0;       // 只在第一次调用时初始化
    ++n;
    std::cout << n << '\n';
}

int main() {
    counter();  // 1
    counter();  // 2
    counter();  // 3
}
```

这种变量在函数第一次执行时初始化，之后**保持上一次的值**，程序结束才销毁。注意多线程下访问要小心（Day80 讲 atomic）。

### 与 C 语言 static 的区别

C 里 static 还用来限定"只在本文件可见"，C++ 中也保留这个语义。今天先聚焦类内 static。

## 核心重点
- 静态成员变量属于类，全类共享一份
- 类内声明 + 类外定义是传统写法
- 静态成员函数没有 this，只能访问静态成员
- 函数内 static 局部变量只初始化一次，跨调用保持
- 用 `类名::成员` 访问静态成员

## 练习/任务
1. 给 Student 类加静态成员 totalCount，跟踪创建了多少个对象
2. 写一个工具类 `MathUtil`，静态方法 `static int max(int,int)`、`static bool isPrime(int)`
3. 写函数用 static 局部变量统计它被调用了多少次
4. （选做）在静态成员函数里尝试访问普通成员变量，观察编译错误

## 打卡清单
- [ ] 已理解静态成员属于类不属于对象
- [ ] 已会在类外定义静态成员变量
- [ ] 已会写静态成员函数
- [ ] 已知道函数内 static 变量只初始化一次
