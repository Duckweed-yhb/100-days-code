# Day 72 · 条件变量与信号量

## 今日目标
- 理解条件变量用于线程间等待/通知
- 掌握 producer-consumer 模型
- 认识信号量

## 学习内容

### 条件变量解决什么问题

互斥锁只保证"同一时间一个人进"。但"队列空了，消费者怎么办"？答案是等，等生产者放数据。条件变量就是干这个的。

### pthread_cond（POSIX/类 Unix 环境 Linux/macOS；Windows 可用 WSL）

```c
pthread_mutex_t mutex = PTHREAD_MUTEX_INITIALIZER;
pthread_cond_t  cond  = PTHREAD_COND_INITIALIZER;
int buffer = 0;
int has_data = 0;

// 消费者
void *consumer(void *arg) {
    pthread_mutex_lock(&mutex);
    while (!has_data) {
        pthread_cond_wait(&cond, &mutex);   // 等待并解锁
    }
    printf("消费: %d\n", buffer);
    has_data = 0;
    pthread_mutex_unlock(&mutex);
    return NULL;
}

// 生产者
void *producer(void *arg) {
    pthread_mutex_lock(&mutex);
    buffer = 42;
    has_data = 1;
    pthread_cond_signal(&cond);   // 通知消费者
    pthread_mutex_unlock(&mutex);
    return NULL;
}
```

### 关键点

- `pthread_cond_wait` 自动释放互斥锁并等待
- 被唤醒时重新加锁
- 用 while 不用 if（防止虚假唤醒）
- `signal` 唤醒一个，`broadcast` 唤醒所有

### 信号量 semaphore

计数版的锁：可以有 N 个线程同时进。POSIX 信号量：

```c
#include <semaphore.h>
sem_t sem;
sem_init(&sem, 0, 2);   // 初始值 2
sem_wait(&sem);          // P 操作，减 1
sem_post(&sem);          // V 操作，加 1
```

### 生产者消费者经典模型

- 缓冲区
- 生产者放数据，满了等
- 消费者取数据，空了等
- 互斥保护缓冲区，条件变量通知状态变化

## 核心重点
- 条件变量用于等待/通知
- cond_wait 自动解锁+等待
- 用 while 循环防虚假唤醒
- signal 唤醒一个，broadcast 唤醒全部
- 信号量是计数版锁

## 练习/任务
1. 实现简单的 producer-consumer
2. 生产者放一个数，消费者打印
3. 理解为什么用 while 不用 if
4. （选做）用信号量实现两个线程交替打印

## 今日刷题
- 今天系统编程为主，不安排算法题。

## 打卡清单
- [ ] 理解条件变量用途
- [ ] 会写 producer-consumer
- [ ] 知道虚假唤醒
- [ ] 认识信号量
