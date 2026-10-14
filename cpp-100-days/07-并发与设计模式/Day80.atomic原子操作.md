# Day 80 · atomic原子操作

## 今日目标
- 理解原子操作为什么不需要锁
- 学会用 std::atomic
- 对比 mutex 与 atomic

## 学习内容

### 原子操作是什么

原子操作是"不可分割"的：线程执行时不会被其他线程打断。`++counter` 这种读-改-写，用原子类型就不需要 mutex。

```cpp
#include <atomic>
#include <iostream>
#include <thread>

std::atomic<int> counter{0};

void f() {
    for (int i = 0; i < 100000; ++i)
        ++counter;
}

int main() {
    std::thread t1(f), t2(f);
    t1.join(); t2.join();
    std::cout << counter;    // 一定是 200000
}
```

### atomic 常用类型

```cpp
std::atomic<int> a{0};
std::atomic<bool> done{false};
std::atomic_flag flag;
```

### 常用操作

- `load()`：读；
- `store(x)`：写；
- `fetch_add(n)` / `++` / `--`；
- `exchange(x)`：写入并返回旧值；
- `compare_exchange_weak/strong`：CAS（比较并交换）。

### atomic vs mutex

| 对比 | atomic | mutex |
|---|---|---|
| 保护对象 | 单个变量 | 一段代码 |
| 性能 | 高（无系统调用） | 较低 |
| 适用 | 简单计数器、标志位 | 复杂临界区 |

### 顺序一致性（了解）

atomic 有 memory_order 参数（relaxed/acquire/release 等），初学阶段用默认的 seq_cst 即可，不需要深究。

### 典型用途

- 计数器；
- 退出标志：`std::atomic<bool> stop{false};`；
- 无锁数据结构。

## 核心重点
- std::atomic 让单变量操作无锁
- 不用写 lock/unlock
- 比 mutex 快，但只适合简单场景
- 退出标志用 atomic<bool>
- 默认内存序够用

## 练习/任务
1. 把 Day78 的 counter 改成 atomic，去掉 mutex
2. 用 atomic<bool> 做线程退出标志：主线程设置 stop=true，子线程检查后退出
3. 对比 atomic 与 mutex 版本的 counter 性能（粗测）
4. （选做）用 fetch_add 实现一个简单计数器

## 今日刷题

- **LeetCode 146. LRU 缓存**。
- **LeetCode 460. LFU 缓存**。
- **LeetCode 208. Trie**。

## 打卡清单
- [ ] 已会用 std::atomic
- [ ] 已理解 atomic 无锁
- [ ] 已会用 atomic 做退出标志
- [ ] 已能区分 atomic 与 mutex 场景
