# Day 88 · 阶段七综合练习

## 今日目标
- 综合运用并发与设计模式
- 写一个带日志的多线程小工具
- 复盘本阶段

## 学习内容

### 本阶段知识地图

| 主题 | 关键 |
|---|---|
| 线程基础 | std::thread、join/detach、std::ref |
| 互斥 | mutex、lock_guard、unique_lock |
| 条件变量 | wait/notify、生产者消费者 |
| 原子 | atomic、无锁计数器 |
| 异步 | async、future |
| 死锁 | 固定顺序、scoped_lock |
| 网络 | socket 概念、TCP 流程 |
| 设计模式 | 单例、工厂、观察者、策略 |

### 综合项目：多线程日志统计

需求：

- 一个 Logger 单例（Day85）；
- 启动 4 个工作线程，每个往 Logger 写日志；
- 用 mutex 保护 Logger 内部输出；
- 主线程等所有线程结束。

骨架：

```cpp
#include <iostream>
#include <mutex>
#include <thread>
#include <vector>
#include <string>

class Logger {
public:
    static Logger& instance() {
        static Logger inst;
        return inst;
    }
    void log(const std::string& msg) {
        std::lock_guard<std::mutex> lock(m_);
        std::cout << msg << '\n';
    }
private:
    Logger() = default;
    std::mutex m_;
};

void worker(int id) {
    for (int i = 0; i < 5; ++i) {
        Logger::instance().log("线程 " + std::to_string(id) +
                               " 第 " + std::to_string(i) + " 条");
    }
}

int main() {
    std::vector<std::thread> threads;
    for (int i = 0; i < 4; ++i) threads.emplace_back(worker, i);
    for (auto& t : threads) t.join();
}
```

### 复盘建议

- 把"什么时候用 mutex、什么时候用 atomic"默写一遍；
- 把单例、工厂、观察者、策略各自的适用场景写下来；
- 下一阶段是综合项目，把所有东西串起来。

## 核心重点
- 并发的核心是保护共享数据
- RAII（lock_guard）让锁不泄漏
- 设计模式是工具，别硬套
- 网络编程今天只懂概念就够

## 练习/任务
1. 完成上面的多线程日志程序
2. 加一个 atomic<int> 统计总日志条数
3. 让 Logger 把日志同时写到文件
4. （选做）用 async 重写，看结果是否一样

## 今日刷题

- **LeetCode 200. 岛屿数量**。
- **LeetCode 133. 克隆图**。
- **LeetCode 207. 课程表**。

## 打卡清单
- [ ] 已完成多线程日志程序
- [ ] 已用 atomic 统计条数
- [ ] 已理解 mutex 保护共享数据
- [ ] 已复盘本阶段所有模式
