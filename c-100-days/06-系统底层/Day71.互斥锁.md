# Day 71 · 互斥锁

## 今日目标
- 理解竞态条件
- 掌握 pthread_mutex 加锁解锁
- 学会用锁保护共享数据

## 学习内容

### 为什么需要锁

counter++ 看似一行，实际是三步：
1. 读 counter 到寄存器
2. 寄存器 +1
3. 写回 counter

两个线程同时做就会丢更新。互斥锁保证同一时间只有一个线程进入临界区。

### pthread_mutex（POSIX/类 Unix 环境 Linux/macOS；Windows 可用 WSL）

```c
#include <pthread.h>

pthread_mutex_t lock = PTHREAD_MUTEX_INITIALIZER;
int counter = 0;

void *add(void *arg) {
    for (int i = 0; i < 100000; i++) {
        pthread_mutex_lock(&lock);
        counter++;
        pthread_mutex_unlock(&lock);
    }
    return NULL;
}
```

### 用法

- `pthread_mutex_lock(&lock)`：上锁，已被占就阻塞
- `pthread_mutex_unlock(&lock)`：解锁
- 谁锁的谁解，不要跨线程
- 加锁后尽快解锁，不要在锁里做慢操作

### 初始化与销毁

```c
pthread_mutex_t lock;
pthread_mutex_init(&lock, NULL);
// 使用...
pthread_mutex_destroy(&lock);
```

`PTHREAD_MUTEX_INITIALIZER` 用于静态初始化。

### 死锁

两个线程各持一把锁，又等对方释放：

```
线程 A: 锁了 mutex1，等 mutex2
线程 B: 锁了 mutex2，等 mutex1
```

永久卡住。避免死锁：
- 按固定顺序加锁
- 用 `pthread_mutex_trylock` 不阻塞
- 减少锁粒度

### 锁粒度

- 太粗：整个函数加锁，并发上不去
- 太细：频繁加解锁开销大
- 只保护真正需要互斥的代码段

## 核心重点
- 竞态：多线程同时改共享数据
- 互斥锁保证同一时间一个线程进临界区
- lock/unlock 成对
- 死锁要避免
- 锁粒度要合适

## 练习/任务
1. 用互斥锁修复 counter++ 问题
2. 两个线程各加 10 万次，结果正确
3. 故意写一个死锁（不跑也行，理解原理）
4. （选做）用锁保护一个银行账户余额

## 今日刷题
- 今天系统编程为主，不安排算法题。

## 打卡清单
- [ ] 理解竞态条件
- [ ] 会用 pthread_mutex
- [ ] 知道死锁原因
- [ ] 会加锁保护共享数据
