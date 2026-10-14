# Day 62 · RAII与异常安全

## 今日目标
- 深入理解 RAII
- 掌握异常安全的三个等级
- 写出异常安全的代码

## 学习内容

### RAII 再谈

Resource Acquisition Is Initialization：**把资源绑在对象生命周期上**。

- 构造时申请资源；
- 析构时释放资源；
- 即使中途抛异常，栈展开也会自动调析构。

```cpp
class FileGuard {
    FILE* f_;
public:
    explicit FileGuard(const char* path) { f_ = fopen(path, "r"); }
    ~FileGuard() { if (f_) fclose(f_); }
    FILE* get() { return f_; }
};
```

### 异常安全等级

- **基本保证**：异常后程序状态合法（不泄漏、不破坏）；
- **强保证**：异常后程序状态与调用前完全一致（要么成功，要么没变化）；
- **不抛保证**：函数保证不抛异常（noexcept）。

### 缺了 RAII 会怎样

```cpp
void bad() {
    int* p = new int(42);
    riskyOperation();       // 如果这里抛异常，p 泄漏
    delete p;
}
```

改成：

```cpp
void good() {
    auto p = std::make_unique<int>(42);
    riskyOperation();       // 即使抛异常，unique_ptr 析构自动 delete
}
```

### copy-and-swap 惯用法

实现强异常安全的 operator=：

```cpp
T& operator=(T other) {       // 传值，可能抛异常但还没动 *this
    swap(*this, other);        // 不抛
    return *this;
}   // other 析构，释放旧资源
```

这个模式今天先认识，能理解思路即可。

### noexcept

承诺函数不抛异常：

```cpp
void may_throw();
void no_throw() noexcept;
```

移动构造、swap、析构都应该是 noexcept，否则 STL 会退回拷贝。

## 核心重点
- RAII：资源绑对象，析构自动释放
- 异常安全三等级：基本、强、不抛
- 智能指针、lock_guard、fstream 都是 RAII
- 移动构造/swap/析构加 noexcept
- 手写管理资源的类 = 给自己挖坑

## 练习/任务
1. 写一个 FileGuard RAII 类
2. 复现"new 了但抛异常导致泄漏"，用 unique_ptr 修复
3. 给 swap 函数加 noexcept
4. （选做）查资料了解 copy-and-swap 惯用法

## 今日刷题

- **LeetCode 46. 全排列**：回溯。
- **LeetCode 78. 子集**：回溯或位运算。
- **LeetCode 22. 括号生成**：DFS。

## 打卡清单
- [ ] 已理解 RAII 本质
- [ ] 已会用 unique_ptr 防异常泄漏
- [ ] 已知道什么时候加 noexcept
- [ ] 已完成至少一道回溯题
