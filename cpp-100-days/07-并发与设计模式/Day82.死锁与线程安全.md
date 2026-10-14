# Day 82 · 死锁与线程安全

## 今日目标
- 理解死锁产生的四个条件
- 学会避免死锁
- 写出线程安全的类

## 学习内容

### 死锁是什么

两个线程各持一把锁，又互相等对方释放：

```cpp
std::mutex m1, m2;

void f() {
    std::lock_guard l1(m1);
    std::lock_guard l2(m2);   // 假设另一处反过来
}
void g() {
    std::lock_guard l1(m2);
    std::lock_guard l2(m1);
}
```

可能 f 持有 m1 等 m2，g 持有 m2 等 m1，永远卡死。

### 死锁四条件

1. 互斥；
2. 持有并等待；
3. 不可剥夺；
4. 循环等待。

破坏任意一个就能避免。

### 避免死锁的方法

1. **固定加锁顺序**：所有线程都按 m1 → m2 的顺序加锁；
2. **用 std::lock 同时加多个锁**：`std::lock(m1, m2)` 不会死锁；
3. **缩小锁粒度**：能不加就不加；
4. **用 try_lock**：拿不到就退避。

### std::lock 与 std::scoped_lock

```cpp
std::scoped_lock lock(m1, m2);   // C++17，同时锁多个
```

### 线程安全的类

一个类线程安全意味着：多个线程同时调用它的 public 方法，结果正确。做法：

- 每个 public 方法都加锁；
- 锁是类的成员（mutable）；
- 不要从 public 方法返回内部可变引用。

```cpp
class Counter {
    int n_ = 0;
    mutable std::mutex m_;
public:
    void inc() {
        std::lock_guard<std::mutex> lock(m_);
        ++n_;
    }
    int get() const {
        std::lock_guard<std::mutex> lock(m_);
        return n_;
    }
};
```

`mutable` 让 const 方法也能锁 mutex。

## 核心重点
- 死锁 = 循环等待
- 固定加锁顺序是最简单的办法
- std::scoped_lock 同时锁多把
- 线程安全类：public 方法加锁，mutex 作成员
- const 方法里要锁 mutex 得用 mutable

## 练习/任务
1. 故意写一段会死锁的代码（别真跑太久）
2. 用固定加锁顺序修复
3. 写一个线程安全的 Counter 类
4. （选做）用 std::scoped_lock 同时锁两个 mutex

## 今日刷题

- **LeetCode 200. 岛屿数量**。
- **LeetCode 133. 克隆图**。
- **LeetCode 207. 课程表**。

## 打卡清单
- [ ] 已理解死锁成因
- [ ] 已会用固定加锁顺序
- [ ] 已会写线程安全类
- [ ] 已知道 mutable mutex
