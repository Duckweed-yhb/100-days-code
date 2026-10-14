# Day 55 · 移动构造与移动赋值实战

## 今日目标
- 系统掌握五法则
- 理解 noexcept 在移动中的重要性
- 在实际类中正确实现移动

## 学习内容

### 五法则完整版

一个管理资源的类，需要：

1. 析构函数 `~T()`；
2. 拷贝构造 `T(const T&)`；
3. 拷贝赋值 `T& operator=(const T&)`；
4. 移动构造 `T(T&&) noexcept`；
5. 移动赋值 `T& operator=(T&&) noexcept`。

### 完整示例：String

```cpp
class String {
    char* data_ = nullptr;
public:
    String(const char* s = "") {
        data_ = new char[std::strlen(s) + 1];
        std::strcpy(data_, s);
    }
    ~String() { delete[] data_; }

    // 拷贝构造
    String(const String& o) {
        data_ = new char[std::strlen(o.data_) + 1];
        std::strcpy(data_, o.data_);
    }
    // 拷贝赋值
    String& operator=(const String& o) {
        if (this == &o) return *this;
        delete[] data_;
        data_ = new char[std::strlen(o.data_) + 1];
        std::strcpy(data_, o.data_);
        return *this;
    }
    // 移动构造
    String(String&& o) noexcept : data_(o.data_) {
        o.data_ = nullptr;
    }
    // 移动赋值
    String& operator=(String&& o) noexcept {
        if (this == &o) return *this;
        delete[] data_;
        data_ = o.data_;
        o.data_ = nullptr;
        return *this;
    }
};
```

### 为什么移动要 noexcept

STL 容器（如 vector）在扩容时，如果元素的移动构造不是 noexcept，编译器为了安全会退回用拷贝构造（因为移动抛异常会导致中间状态无法回滚）。所以写移动函数一定加 `noexcept`。

### = default 简化

如果你的类成员都是可移动的（string、vector、unique_ptr），直接让编译器生成：

```cpp
class MyClass {
    std::string name_;
    std::vector<int> data_;
public:
    MyClass() = default;
    // 编译器自动生成五法则，且都是正确的
};
```

**优先用 RAII 成员，别自己写裸指针。**

## 核心重点
- 五法则：析构、拷贝构造、拷贝赋值、移动构造、移动赋值
- 移动函数加 noexcept
- 优先用 string/vector/unique_ptr 成员，让编译器自动生成
- 移动后原对象要置空，避免 double free

## 练习/任务
1. 把上面的 String 类敲一遍，写 main 测试拷贝与移动
2. 在移动构造和拷贝构造里加打印，验证 vector push_back 扩容时调移动
3. 写一个类用 vector<string> 作成员，验证编译器自动生成移动
4. （选做）把移动构造的 noexcept 去掉，观察 vector 扩容时是否退回拷贝

## 今日刷题

- **LeetCode 739. 每日温度**：单调栈模板题。
- **LeetCode 84. 柱状图中最大的矩形**：单调栈经典题。
- **LeetCode 42. 接雨水**：双指针或单调栈。

## 打卡清单
- [ ] 已能写全五法则
- [ ] 已理解 noexcept 的作用
- [ ] 已优先用 RAII 成员代替裸指针
- [ ] 已完成至少一道单调栈题
