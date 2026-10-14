# Day 70 · 线程入门pthread

## 今日目标
- 理解线程与进程的区别
- 会用 pthread 创建线程
- 理解线程共享地址空间

## 学习内容

### 进程 vs 线程

| 方面 | 进程 | 线程 |
|---|---|---|
| 内存 | 独立 | 共享 |
| 创建开销 | 大 | 小 |
| 切换 | 慢 | 快 |
| 通信 | IPC 复杂 | 直接共享变量 |
| 崩溃影响 | 自己崩 | 一个崩整个崩 |

线程是"轻量级进程"，同一进程内的多个线程共享代码、数据、堆，但各自有栈。

### pthread 创建线程（POSIX/类 Unix 环境 Linux/macOS；Windows 可用 WSL）

```c
#include <pthread.h>
#include <stdio.h>
#include <unistd.h>

void *worker(void *arg) {
    int id = *(int*)arg;
    printf("线程 %d 工作中\n", id);
    return NULL;
}

int main(void) {
    pthread_t tids[3];
    int ids[3] = {1, 2, 3};
    for (int i = 0; i < 3; i++) {
        pthread_create(&tids[i], NULL, worker, &ids[i]);
    }
    for (int i = 0; i < 3; i++) {
        pthread_join(tids[i], NULL);
    }
    printf("全部结束\n");
    return 0;
}
```

编译要加 `-pthread`：

```bash
gcc thread.c -o thread -pthread
```

### 关键函数

- `pthread_create`：创建线程，执行函数
- `pthread_join`：等待线程结束
- `pthread_exit`：线程退出

### 线程共享什么

- 全局变量
- 堆内存
- 代码
- 文件描述符

### 线程不共享什么

- 栈（局部变量）
- 寄存器
- 线程 ID

### 竞态条件初现

多个线程同时改一个全局变量，结果不确定。明天学互斥锁解决。

```c
int counter = 0;
void *add(void *arg) {
    for (int i = 0; i < 100000; i++) counter++;
    return NULL;
}
// 两个线程各加 10 万，counter 不一定是 20 万
```

## 核心重点
- 线程共享地址空间，切换快
- pthread_create 创建，pthread_join 等待
- 线程共享全局/堆，不共享栈
- 多线程改共享数据会竞态
- 编译加 -pthread

## 练习/任务
1. 创建 3 个线程，每个打印自己的 ID
2. 创建两个线程同时 counter++，观察结果不对
3. 用 pthread_join 等待线程结束
4. （选做）理解为什么 counter++ 不是原子的

## 今日刷题
- 今天系统编程为主，不安排算法题。

## 打卡清单
- [ ] 区分进程与线程
- [ ] 会用 pthread_create
- [ ] 理解线程共享资源
- [ ] 观察到竞态条件
