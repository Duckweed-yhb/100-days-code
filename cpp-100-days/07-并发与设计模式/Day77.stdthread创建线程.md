# Day 77 · stdthread创建线程

## 今日目标
- 学会用 std::thread 启动线程
- 学会 join 与 detach
- 理解线程函数的参数传递

## 学习内容

### 第一个线程

```cpp
#include <iostream>
#include <thread>

void worker(int id) {
    std::cout << "线程 " << id << " 工作中\n";
}

int main() {
    std::thread t(worker, 1);    // 启动线程
    t.join();                     // 等 t 结束
    std::cout << "主线程结束\n";
}
```

### 可调用对象传给 thread

`std::thread` 接收任意可调用对象：函数、lambda、函数对象、成员函数指针。

```cpp
std::thread t([](){ std::cout << "lambda 线程\n"; });
t.join();
```

### join 与 detach

- `t.join()`：主线程等 t 结束再继续；
- `t.detach()`：把 t 丢到后台，主线程不再管它。

**必须二选一**。线程对象析构前没 join 也没 detach，程序直接终止（std::terminate）。

### 传参

```cpp
void f(int x, const std::string& s) { ... }

std::thread t(f, 1, "hello");
```

注意：thread 构造时默认按值拷贝参数。要传引用要用 `std::ref`：

```cpp
void f(int& x) { ++x; }
int n = 0;
std::thread t(f, std::ref(n));
t.join();
// n == 1
```

### 主线程退出

主线程 return 时，所有其他线程被强行终止。所以主线程要 join 所有子线程。

### hardware_concurrency

```cpp
unsigned n = std::thread::hardware_concurrency();
std::cout << "可用核数：" << n;
```

## 核心重点
- std::thread t(可调用对象, 参数...)
- join 等待，detach 后台跑
- 必须 join 或 detach，否则 terminate
- 引用参数要包 std::ref
- 主线程退出会杀掉所有子线程

## 练习/任务
1. 启动两个线程，分别打印 1~5
2. 用 lambda 写线程入口
3. 用 std::ref 让线程修改外部变量
4. （选做）启动 N 个线程并发打印自己的 id

## 今日刷题

- **LeetCode 200. 岛屿数量**。
- **LeetCode 994. 腐烂的橘子**。
- **LeetCode 133. 克隆图**。

## 打卡清单
- [ ] 已会创建并 join 线程
- [ ] 已会用 lambda 作线程函数
- [ ] 已会用 std::ref 传引用
- [ ] 已理解不 join 的后果
