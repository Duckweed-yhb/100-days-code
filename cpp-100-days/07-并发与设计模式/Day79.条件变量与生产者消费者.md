# Day 79 · 条件变量与生产者消费者

## 今日目标
- 学会用 std::condition_variable
- 实现经典生产者-消费者模型
- 理解 wait/notify

## 学习内容

### 为什么需要条件变量

光有锁不够：消费者想等"队列非空"，又不能一直循环抢锁。条件变量让线程可以"睡"到条件满足再被唤醒。

### 三个关键函数

- `wait(lock, 谓词)`：释放锁并休眠，被唤醒后重新加锁；
- `notify_one()`：唤醒一个等待者；
- `notify_all()`：唤醒所有等待者。

### 生产者消费者骨架

```cpp
#include <iostream>
#include <queue>
#include <mutex>
#include <thread>
#include <condition_variable>

std::queue<int> q;
std::mutex m;
std::condition_variable cv;

void producer() {
    for (int i = 0; i < 10; ++i) {
        {
            std::lock_guard<std::mutex> lock(m);
            q.push(i);
        }
        cv.notify_one();
    }
}

void consumer() {
    for (int i = 0; i < 10; ++i) {
        std::unique_lock<std::mutex> lock(m);
        cv.wait(lock, []{ return !q.empty(); });
        int x = q.front();
        q.pop();
        std::cout << "消费 " << x << '\n';
    }
}
```

注意：

- wait 必须配合 `std::unique_lock`（不像 lock_guard 是只读的）；
- wait 第二个参数是谓词，防止"假唤醒"。

### 为什么要 unique_lock

lock_guard 一旦构造就锁死，不能 unlock。条件变量的 wait 需要中途释放锁、醒来再加锁，所以用 unique_lock。

### 假唤醒

系统可能无故唤醒线程，所以 wait 一定要写谓词：`cv.wait(lock, []{ return !q.empty(); })`，醒来再检查一次条件。

## 核心重点
- condition_variable 用于线程间等待/通知
- wait 配 unique_lock + 谓词
- notify_one 唤醒一个
- 生产者消费者是经典模型
- 一定要处理假唤醒

## 练习/任务
1. 实现上面的生产者-消费者
2. 让生产者生产 100 个数，2 个消费者消费
3. 故意不加谓词，观察假唤醒的影响
4. （选做）加一个"队列满了生产者等待"的逻辑

## 今日刷题

- **LeetCode 207. 课程表**：拓扑。
- **LeetCode 210. 课程表 II**。
- **LeetCode 208. Trie**。

## 打卡清单
- [ ] 已会用 condition_variable
- [ ] 已实现生产者消费者
- [ ] 已理解 unique_lock 与 lock_guard 区别
- [ ] 已知道假唤醒问题
