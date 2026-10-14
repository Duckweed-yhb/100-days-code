# Day 81 · future与async

## 今日目标
- 学会用 std::async 启动异步任务
- 用 std::future 拿结果
- 理解与 thread 的区别

## 学习内容

### std::async

`std::async` 启动一个异步任务，返回一个 `std::future`：

```cpp
#include <future>
#include <iostream>

int slowAdd(int a, int b) {
    std::this_thread::sleep_for(std::chrono::seconds(1));
    return a + b;
}

int main() {
    std::future<int> fut = std::async(slowAdd, 1, 2);
    std::cout << "做别的事...\n";
    std::cout << "结果：" << fut.get() << '\n';   // 等任务完成
}
```

- `fut.get()`：阻塞等结果，返回值；
- `fut.wait()`：只等不取。

### 与 std::thread 区别

- `std::thread`：你自己管理线程，结果通过共享变量返回；
- `std::async`：库帮你管理，结果通过 future 返回，更省心。

### 启动策略

```cpp
std::async(std::launch::async, task);           // 必须立即开新线程
std::async(std::launch::deferred, task);        // 延迟到 get 时才执行
```

默认是两者择一，系统自己决定。

### 异常传播

如果 task 抛异常，`fut.get()` 会重新抛出：

```cpp
try {
    fut.get();
} catch (const std::exception& e) {
    std::cout << "异步任务出错：" << e.what();
}
```

### 适用场景

- 后台计算，主线程继续干别的；
- 并行算几个独立任务；
- 不想手动管理 thread 生命周期。

## 核心重点
- std::async 启动异步任务，返回 future
- fut.get() 阻塞拿结果
- 比 std::thread 高层、省心
- 异常会通过 future 重新抛出
- 适合"发后即忘"或"并行算多个任务"

## 练习/任务
1. 用 async 启动一个耗时任务，主线程同时做别的事
2. 并行启动 3 个 async，分别算不同任务，get 所有结果
3. 让 async 任务抛异常，用 try/catch 捕获
4. （选做）用 async 并行求 4 个数组的和

## 今日刷题

- **LeetCode 200. 岛屿数量**。
- **LeetCode 994. 腐烂的橘子**。
- **LeetCode 127. 单词接龙**：BFS。

## 打卡清单
- [ ] 已会用 std::async
- [ ] 已会用 future.get()
- [ ] 已理解与 thread 的区别
- [ ] 已会捕获异步异常
