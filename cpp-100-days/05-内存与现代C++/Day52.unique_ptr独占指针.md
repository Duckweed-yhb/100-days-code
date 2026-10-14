# Day 52 · unique_ptr独占指针

## 今日目标
- 理解智能指针为什么出现
- 掌握 `std::unique_ptr`
- 用它替代裸 new/delete

## 学习内容

### 裸指针的问题

```cpp
void f() {
    int* p = new int(42);
    // ... 中途 return、抛异常、忘记 delete
    delete p;
}
```

只要程序复杂，就会有人忘 delete。智能指针把"delete"绑在对象析构上，自动释放。

### unique_ptr 是什么

`std::unique_ptr<T>` 表示"独占所有权"：这个指针拥有对象，它销毁时自动 delete 对象。

```cpp
#include <memory>

void f() {
    std::unique_ptr<int> p(new int(42));
    std::cout << *p;
}   // p 离开作用域自动 delete
```

### make_unique（C++14）

更推荐的写法：

```cpp
auto p = std::make_unique<int>(42);
auto v = std::make_unique<std::vector<int>>(10, 0);
```

### 不能拷贝，只能移动

unique_ptr 独占所有权，**不能拷贝**：

```cpp
auto p1 = std::make_unique<int>(1);
auto p2 = p1;             // 错：不能拷贝
auto p3 = std::move(p1);  // 对：转移所有权，p1 变空
```

### 用作函数参数/返回值

```cpp
std::unique_ptr<int> factory() {
    return std::make_unique<int>(42);   // 自动 move
}

void takeOwnership(std::unique_ptr<int> p) {
    // 这里拥有 p
}

auto p = factory();
takeOwnership(std::move(p));   // 所有权移交
```

如果只是想访问对象，不转移所有权，传 `T*` 或 `T&`：

```cpp
void visit(int* p) { if (p) std::cout << *p; }
```

### 管理数组

```cpp
auto arr = std::make_unique<int[]>(100);   // 自动 delete[]
arr[0] = 1;
```

### 什么时候用 unique_ptr

- 你明确拥有一块堆内存；
- 不想手动 delete；
- 不需要多个指针共享同一对象。

## 核心重点
- unique_ptr 独占所有权，析构自动 delete
- 优先用 make_unique
- 不能拷贝，只能 std::move
- 不转移所有权时传 T* / T&
- 管理数组用 unique_ptr<T[]>

## 练习/任务
1. 用 unique_ptr 重写 Day12 的动态数组例子
2. 写函数返回 unique_ptr<int>，main 中接收并使用
3. 写函数接收 unique_ptr<int>，验证所有权转移后原指针变空
4. （选做）用 unique_ptr<File> 管理打开的文件

## 今日刷题

- **LeetCode 100. 相同的树**：递归比较两棵树结构与值。
- **LeetCode 101. 对称二叉树**：递归比较左右子树。
- **LeetCode 104. 二叉树最大深度**：后序遍历。

## 打卡清单
- [ ] 已会用 make_unique
- [ ] 已理解 unique_ptr 不能拷贝
- [ ] 已会用 std::move 转移所有权
- [ ] 已开始用智能指针代替裸 new
