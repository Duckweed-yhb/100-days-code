# Day 36 · RTTI与类型转换

## 今日目标
- 了解 RTTI（运行时类型信息）是什么
- 学会用 `dynamic_cast` 安全向下转换
- 区分 static_cast / dynamic_cast / const_cast / reinterpret_cast

## 学习内容

### 为什么需要 RTTI

多态体系里，基类指针实际上指向哪个派生类对象？C++ 提供 RTTI（Run-Time Type Information）让你在运行时查询对象类型。

### dynamic_cast

`dynamic_cast<Dog*>(p)` 在运行时检查 `p` 指向的对象是不是真的 Dog：

```cpp
Animal* p = new Dog;
Dog* dp = dynamic_cast<Dog*>(p);
if (dp) dp->bark();      // 成功
```

如果 p 实际不是 Dog，返回 `nullptr`（指针版本）或抛 `std::bad_cast`（引用版本）。

### typeid

`typeid` 可以看对象的真实类型：

```cpp
#include <typeinfo>

Animal* p = new Dog;
std::cout << typeid(*p).name();    // 输出类似 "3Dog"（编译器相关）
```

### 四种强制转换

| 转换 | 用途 |
|---|---|
| `static_cast<T>(x)` | 普通向上/向下转换，编译期检查，不查类型 |
| `dynamic_cast<T>(x)` | 多态体系内安全向下转换，运行时检查 |
| `const_cast<T>(x)` | 加/去掉 const |
| `reinterpret_cast<T>(x)` | 重新解释比特位，最危险，极少用 |

现代 C++ 不推荐 C 风格 `(T)x`，因为它会偷偷做上面任意一种转换。

### 经验法则

- 优先用虚函数做事情，而不是 `dynamic_cast` 一堆 if-else 判断类型；
- 真要判断类型时，用 `dynamic_cast`，不要自己写 typeid 链；
- `reinterpret_cast` 只在和硬件/外部接口打交道时才用。

## 核心重点
- RTTI 让程序在运行时查询对象真实类型
- dynamic_cast 安全向下转换，失败返回 nullptr
- static_cast 不做运行时检查
- 优先用虚函数而非类型判断
- 别用 C 风格强转

## 练习/任务
1. 写一个 `Animal*` 指向不同派生类对象，用 dynamic_cast 判断它是 Dog 还是 Cat
2. 对比 static_cast 和 dynamic_cast：把 Animal* 转成 Dog*，分别在错误的对象上试
3. 用 typeid 打印几个对象的类型名
4. （选做）故意把 Animal* 转成错误的派生类，观察 dynamic_cast 返回 nullptr

## 今日刷题

- **LeetCode 146. LRU 缓存**：哈希表 + 双向链表。get/put 都要 O(1)：访问节点时把它移到链表头，超容量时删链表尾。
- **LeetCode 240. 搜索二维矩阵 II**：从右上角开始，比 target 小就往下走，比 target 大就往左走。
- **LeetCode 54. 螺旋矩阵**：按层模拟，上下左右四个边界往里收缩。

## 打卡清单
- [ ] 已会用 dynamic_cast 安全向下转换
- [ ] 已分清四种 cast
- [ ] 已理解 RTTI 概念
- [ ] 已完成至少一道中难刷题
