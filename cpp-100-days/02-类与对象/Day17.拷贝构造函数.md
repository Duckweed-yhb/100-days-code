# Day 17 · 拷贝构造函数

## 今日目标
- 理解拷贝构造函数什么时候被调用
- 区分默认浅拷贝与深拷贝
- 学会在需要时自定义拷贝构造函数

## 学习内容

### 什么是拷贝构造

用一个已存在的对象去初始化一个新对象时，会调用**拷贝构造函数**：

```cpp
class Student {
public:
    std::string name;
    Student(const std::string& n) : name(n) {}
};

Student a("张三");
Student b = a;     // 拷贝构造（不是赋值！）
Student c(a);      // 也是拷贝构造
```

如果你不写拷贝构造，编译器会自动生成一个"默认版本"：把每个成员逐个拷贝过去。

### 默认拷贝是浅拷贝

如果类里有指针成员，问题就来了：

```cpp
class Buffer {
public:
    int* data;
    Buffer(int n) { data = new int[n]; }
    ~Buffer() { delete[] data; }
};

Buffer b1(10);
Buffer b2 = b1;     // 默认拷贝：b2.data == b1.data（同一个地址！）
// 出作用域时：b2 析构 delete[]，b1 析构又 delete[] 同一块内存 -> 崩溃
```

两个对象的指针指向**同一块堆内存**，析构时释放两次，这就是浅拷贝的灾难。

### 深拷贝：自定义拷贝构造

```cpp
class Buffer {
public:
    int* data;
    int size;
    Buffer(int n) : size(n) { data = new int[n]; }
    ~Buffer() { delete[] data; }

    // 拷贝构造：重新申请一块内存，把内容复制过去
    Buffer(const Buffer& other) : size(other.size) {
        data = new int[size];
        for (int i = 0; i < size; ++i) data[i] = other.data[i];
    }
};
```

这样 b1 和 b2 各自拥有独立内存，析构互不影响。

### 拷贝构造被调用的三种场景

1. 用同类型对象初始化新对象：`Student b = a;`
2. 函数**按值传参**：`void f(Student s);` 传参时会拷贝
3. 函数**按值返回**：`Student f() { return s; }`（现代编译器有返回值优化，可能跳过拷贝）

### 经验法则

- 类里没有指针/资源成员：用默认拷贝就行；
- 类里有自己 `new` 出来的资源：必须自己写拷贝构造、拷贝赋值、析构（三/五法则，Day19 讲）。

## 核心重点
- 拷贝构造用同类对象初始化新对象时调用
- 编译器默认生成的是浅拷贝
- 有指针成员时必须自定义拷贝构造做深拷贝
- 按值传参、按值返回都会触发拷贝
- 浅拷贝 + 析构释放 = 双重释放崩溃

## 练习/任务
1. 写一个 `MyString` 类，内部用 `char*` 存字符串，自定义深拷贝构造
2. 不写拷贝构造，故意制造"两个对象同一块指针"的崩溃，再用深拷贝修好
3. 给 Buffer 类加一个 `print()` 方法，验证深拷贝后修改一个对象不影响另一个
4. （选做）在拷贝构造函数里打印一行日志，观察它在 main 中被调用了几次

## 打卡清单
- [ ] 已理解拷贝构造的调用时机
- [ ] 已分清浅拷贝与深拷贝
- [ ] 已会写深拷贝构造函数
- [ ] 已知道什么时候必须自定义拷贝构造
