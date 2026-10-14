# Day 16 · 构造函数与析构函数

## 今日目标
- 理解构造函数的作用与写法
- 掌握初始化列表
- 理解析构函数的调用时机

## 学习内容

### 构造函数

对象创建时自动调用的函数，名字和类名相同，没有返回类型：

```cpp
class Student {
private:
    std::string name_;
    int age_;
public:
    // 构造函数
    Student(const std::string& name, int age)
        : name_(name), age_(age) {}    // 初始化列表

    void introduce() const {
        std::cout << name_ << ' ' << age_ << '\n';
    }
};

int main() {
    Student s("张三", 20);    // 自动调用构造函数
    s.introduce();
}
```

### 初始化列表

冒号后面的 `: name_(name), age_(age)` 叫**初始化列表**，比在函数体内赋值更高效：

```cpp
// 推荐写法
Student(const std::string& name, int age) : name_(name), age_(age) {}

// 也能跑，但稍微低效：先默认构造再赋值
Student(const std::string& name, int age) {
    name_ = name;
    age_ = age;
}
```

注意：**const 成员和引用成员必须在初始化列表里初始化**，不能在函数体内赋值。

### 构造函数重载

一个类可以有多个构造函数：

```cpp
class Student {
public:
    Student() : name_("匿名"), age_(0) {}                    // 默认构造
    Student(const std::string& name) : name_(name), age_(0) {}
    Student(const std::string& name, int age) : name_(name), age_(age) {}
};
```

调用时根据实参自动匹配。

### 析构函数

对象销毁时自动调用，名字是 `~类名()`，没有参数也没有返回值：

```cpp
class Demo {
public:
    Demo()  { std::cout << "构造\n"; }
    ~Demo() { std::cout << "析构\n"; }
};

int main() {
    Demo d;
    std::cout << "main 结束前\n";
}
// 输出：构造 -> main 结束前 -> 析构
```

析构函数常用于释放资源（`delete`、关文件、释放锁）。

### 对象销毁时机

- 栈对象：出作用域时自动析构；
- 堆对象：`delete` 时析构；
- 全局对象：程序结束时析构。

```cpp
void f() {
    Demo d;          // 构造
    Demo* p = new Demo;   // 构造
    delete p;        // 析构 p
}                    // d 自动析构
```

## 核心重点
- 构造函数与类同名，无返回类型，创建对象时自动调用
- 推荐用初始化列表 `: 成员(值)` 初始化成员
- 构造函数可重载
- 析构函数 `~类名()`，对象销毁时自动调用
- 栈对象出作用域自动析构，堆对象要 delete

## 练习/任务
1. 给 Day15 的 BankAccount 写构造函数，初始余额由参数给定
2. 写一个类，构造函数打印"创建"，析构函数打印"销毁"，在 main 中观察调用顺序
3. 为 Student 类写三个构造函数：默认、只给名字、给名字和年龄
4. （选做）在函数里 new 一个对象但不 delete，观察析构函数是否被调用

## 打卡清单
- [ ] 已会写带初始化列表的构造函数
- [ ] 已理解构造函数可重载
- [ ] 已知道析构函数的调用时机
- [ ] 已能预测栈对象与堆对象的析构顺序
