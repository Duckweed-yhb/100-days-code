# Day 53 · shared_ptr与weak_ptr

## 今日目标
- 理解共享所有权
- 掌握 `std::shared_ptr`
- 了解 `std::weak_ptr` 解决循环引用

## 学习内容

### shared_ptr 是什么

多个指针可以共享同一对象，内部用**引用计数**：最后一个 shared_ptr 销毁时，对象才 delete。

```cpp
#include <memory>

auto p1 = std::make_shared<int>(42);
{
    auto p2 = p1;          // 拷贝，引用计数变 2
    std::cout << *p2;
}                          // p2 销毁，计数变 1
std::cout << *p1;          // 对象还在
```                          // p1 销毁，计数变 0，delete

### make_shared

```cpp
auto p = std::make_shared<int>(42);
```

比 `shared_ptr<int>(new int(42))` 更高效（一次内存分配）。

### 引用计数查看

```cpp
std::cout << p.use_count();   // 当前引用数
```

### 循环引用问题

```cpp
struct Node {
    std::shared_ptr<Node> next;
    std::shared_ptr<Node> prev;
};
auto a = std::make_shared<Node>();
auto b = std::make_shared<Node>();
a->next = b;
b->prev = a;
// 引用计数都 >= 2，析构时永远减不到 0 -> 泄漏
```

### weak_ptr 破局

`std::weak_ptr<T>` 是"弱引用"，不增加引用计数：

```cpp
struct Node {
    std::shared_ptr<Node> next;
    std::weak_ptr<Node> prev;    // 改成 weak
};
```

使用 weak_ptr 前要 `lock()` 提升为 shared_ptr：

```cpp
if (auto sp = prev.lock()) {
    // sp 是 shared_ptr，对象还活着
}
```

### 三种智能指针怎么选

| 指针 | 用途 |
|---|---|
| unique_ptr | 默认首选，独占 |
| shared_ptr | 共享所有权，开销稍大 |
| weak_ptr | 打破循环引用，观察者 |

## 核心重点
- shared_ptr 共享所有权，引用计数
- make_shared 更高效
- 循环引用导致泄漏，用 weak_ptr 打破
- weak_ptr 使用前要 lock()
- 默认用 unique_ptr，真要共享才用 shared_ptr

## 练习/任务
1. 用 shared_ptr 让两个对象共享同一个 string
2. 复现循环引用泄漏，用 weak_ptr 修复
3. 查看 use_count 变化
4. （选做）写一个 Observer 模式：Subject 持有 weak_ptr<Observer>

## 今日刷题

- **LeetCode 138. 复制带随机指针的链表**：用哈希表存旧节点->新节点映射。
- **LeetCode 146. LRU 缓存**：哈希 + 双向链表。
- **LeetCode 173. 二叉搜索树迭代器**：用栈模拟中序遍历。

## 打卡清单
- [ ] 已会用 make_shared
- [ ] 已理解引用计数
- [ ] 已会用 weak_ptr 破循环引用
- [ ] 已能在三种智能指针间做选择
