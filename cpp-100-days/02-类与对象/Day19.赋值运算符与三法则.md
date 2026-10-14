# Day 19 · 赋值运算符与三法则

## 今日目标
- 理解拷贝赋值运算符 `operator=`
- 掌握"三/五法则"
- 学会写下标运算符 `operator[]`

## 学习内容

### 拷贝构造 vs 拷贝赋值

- 拷贝构造：用一个已有对象**初始化**一个新对象；
- 拷贝赋值：把一个已有对象**覆盖**到另一个已有对象上。

```cpp
Student a("张三");
Student b = a;     // 拷贝构造（b 之前不存在）
Student c;
c = a;             // 拷贝赋值（c 已存在）
```

### 自己写 operator=

```cpp
class Buffer {
    int* data_;
    int size_;
public:
    Buffer(int n) : size_(n), data_(new int[n]) {}
    ~Buffer() { delete[] data_; }

    Buffer(const Buffer& o) : size_(o.size_), data_(new int[o.size_]) {
        for (int i = 0; i < size_; ++i) data_[i] = o.data_[i];
    }

    Buffer& operator=(const Buffer& o) {
        if (this == &o) return *this;          // 1. 自赋值检查
        delete[] data_;                         // 2. 释放旧资源
        size_ = o.size_;
        data_ = new int[size_];                 // 3. 申请新资源
        for (int i = 0; i < size_; ++i) data_[i] = o.data_[i];
        return *this;                           // 4. 返回 *this 支持链式
    }
};
```

### 三法则与五法则

如果一个类需要自定义以下任意一个，通常就需要自定义全部：

- 析构函数
- 拷贝构造函数
- 拷贝赋值运算符

这叫**三法则**。C++11 起又加了移动构造和移动赋值（Day54 讲），合称**五法则**。

如果你写了析构函数来释放资源，却没写拷贝构造和赋值，多半会浅拷贝出 bug。

### 下标运算符 operator[]

让自定义容器能像数组一样 `v[i]` 访问：

```cpp
class IntVector {
    int* data_;
    int size_;
public:
    IntVector(int n) : size_(n), data_(new int[n]) {}
    ~IntVector() { delete[] data_; }

    int& operator[](int i) { return data_[i]; }             // 可读写
    int  operator[](int i) const { return data_[i]; }       // const 对象只读
};

int main() {
    IntVector v(3);
    v[0] = 10;
    v[1] = 20;
    std::cout << v[0] + v[1];
}
```

提供两个版本（非 const / const）是为了让 const 对象也能安全读。

### = default 与 = delete

C++11 起可以显式要求编译器生成或禁止某个函数：

```cpp
class NoCopy {
public:
    NoCopy() = default;
    NoCopy(const NoCopy&) = delete;             // 禁止拷贝
    NoCopy& operator=(const NoCopy&) = delete;
};
```

## 核心重点
- 拷贝构造初始化新对象，拷贝赋值覆盖已有对象
- operator= 要处理自赋值、释放旧资源、返回 *this
- 三法则：析构、拷贝构造、拷贝赋值要么都写，要么都不写
- operator[] 提供读写两个版本
- = delete 可以禁止某个函数

## 练习/任务
1. 给 Day17 的 Buffer 类补全 operator=
2. 在 operator= 里打印日志，验证自赋值 `b = b` 不会崩
3. 给 IntVector 加 size() 方法，并实现 operator[] 的两个版本
4. （选做）用 = delete 写一个不能被拷贝的类，尝试拷贝它观察编译错误

## 打卡清单
- [ ] 已分清拷贝构造与拷贝赋值
- [ ] 已理解三法则
- [ ] 已会写带自赋值检查的 operator=
- [ ] 已会写 operator[]
