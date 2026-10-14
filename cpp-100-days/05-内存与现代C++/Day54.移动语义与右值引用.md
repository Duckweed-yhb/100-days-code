# Day 54 · 移动语义与右值引用

## 今日目标
- 理解左值与右值
- 学会用 `std::move`
- 理解移动语义为什么能提升性能

## 学习内容

### 左值与右值

- **左值**：有名字、能取地址的东西；
- **右值**：临时的、没名字的东西（即将销毁）。

```cpp
int a = 10;       // a 是左值，10 是右值
int b = a + 1;    // a+1 是右值
```

### 右值引用 T&&

C++11 引入 `T&&` 专门绑定右值：

```cpp
int&& r = 10;     // r 绑定右值 10
int& l = a;       // 左值引用
```

关键作用：**告诉编译器"这个对象马上要被销毁，你可以偷它的资源"**。

### std::move

`std::move(x)` 把左值强制转成右值引用，意思是"我同意你偷 x 的资源"：

```cpp
std::string a = "hello";
std::string b = std::move(a);   // 移动：把 a 的内部指针直接给 b
// a 现在处于"合法但未指定"状态，不要再用它
```

### 移动 vs 拷贝

- 拷贝：新申请一块内存，把内容复制过去（贵）；
- 移动：直接把对方的指针"偷"过来，把对方置空（O(1)）。

对 vector、string 这种内部有堆内存的对象，移动比拷贝快得多。

### 移动构造与移动赋值

类可以定义移动构造函数：

```cpp
class Buffer {
    int* data_;
public:
    Buffer(Buffer&& o) noexcept : data_(o.data_) {
        o.data_ = nullptr;      // 偷过来，把 o 置空
    }
    Buffer& operator=(Buffer&& o) noexcept {
        if (this != &o) {
            delete[] data_;
            data_ = o.data_;
            o.data_ = nullptr;
        }
        return *this;
    }
};
```

### 什么时候自动移动

函数按值返回局部对象时，编译器优先移动（甚至 RVO 优化直接构造）：

```cpp
std::vector<int> makeVec() {
    std::vector<int> v(1000);
    return v;     // 自动移动，不拷贝
}
```

## 核心重点
- 左值有名字，右值是临时量
- T&& 右值引用绑定临时对象
- std::move 只是转类型，不真的移动
- 移动构造偷走资源指针，比深拷贝快
- 返回局部对象会自动移动

## 练习/任务
1. 写一个类，同时有拷贝构造和移动构造，打印日志观察调用时机
2. 用 std::move 把一个 vector 转移给另一个
3. 给 Day51 的 MyString 加移动构造
4. （选做）对比拷贝 100 万元素 vector 与移动的耗时

## 今日刷题

- **LeetCode 54. 螺旋矩阵**：按层模拟。
- **LeetCode 48. 旋转图像**：先转置再每行翻转。
- **LeetCode 240. 搜索二维矩阵 II**：从右上角开始走。

## 打卡清单
- [ ] 已分清左值右值
- [ ] 已会用 std::move
- [ ] 已理解移动构造偷资源
- [ ] 已体会到移动比拷贝快
