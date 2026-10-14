# Day 78 · 互斥与lockguard

## 今日目标
- 理解数据竞争
- 学会用 std::mutex 加锁
- 用 lock_guard 自动管理锁

## 学习内容

### 数据竞争

```cpp
int counter = 0;
void f() {
    for (int i = 0; i < 100000; ++i) ++counter;
}

int main() {
    std::thread t1(f), t2(f);
    t1.join(); t2.join();
    std::cout << counter;   // 可能不是 200000！
}
```

`++counter` 看起来一行，实际是"读-改-写"三步，两个线程交错就会丢更新。

### 用 mutex 加锁

```cpp
#include <mutex>

int counter = 0;
std::mutex m;

void f() {
    for (int i = 0; i < 100000; ++i) {
        m.lock();
        ++counter;
        m.unlock();
    }
}
```

同一时刻只有一个线程能持有 mutex，其他线程在 `lock()` 处阻塞。

### lock_guard 自动解锁

手动 lock/unlock 容易忘（尤其抛异常时）。用 `std::lock_guard`：

```cpp
void f() {
    for (int i = 0; i < 100000; ++i) {
        std::lock_guard<std::mutex> lock(m);   // 构造时加锁
        ++counter;
    }   // 出作用域自动 unlock
}
```

这就是 RAII 在并发里的应用。

### 锁的粒度

- 锁太大：并发度低；
- 锁太小：保护不到。

经验：只锁真正需要互斥的临界区。

### 死锁预告

两个线程各持一把锁，又互相等对方的锁，就死锁。明天讲怎么避免。

## 核心重点
- 数据竞争：多线程同时读写共享变量
- std::mutex 互斥锁
- lock_guard 构造加锁、析构解锁
- 临界区越小越好
- 下一话题：死锁与条件变量

## 练习/任务
1. 复现上面 counter 不是 200000 的问题
2. 用 mutex 修复
3. 用 lock_guard 改写
4. （选做）试 4 个线程各加 100000，结果对吗

## 今日刷题

- **LeetCode 200. 岛屿数量**。
- **LeetCode 994. 腐烂的橘子**。
- **LeetCode 695. 岛屿的最大面积**。

## 打卡清单
- [ ] 已复现数据竞争
- [ ] 已会用 mutex
- [ ] 已会用 lock_guard
- [ ] 已理解 RAII 锁
