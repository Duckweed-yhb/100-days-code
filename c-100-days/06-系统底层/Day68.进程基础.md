# Day 68 · 进程基础

## 今日目标
- 理解程序与进程的区别
- 知道 fork 创建子进程（POSIX）
- 理解进程 ID 与状态

## 学习内容

### 程序 vs 进程

- 程序：磁盘上的可执行文件，静态
- 进程：运行中的程序，动态，有自己的内存、状态、PID

### fork 创建进程（POSIX/类 Unix 环境 Linux/macOS；Windows 可用 WSL）

```c
#include <sys/types.h>
#include <unistd.h>
#include <stdio.h>

int main(void) {
    pid_t pid = fork();
    if (pid == 0) {
        // 子进程
        printf("我是子进程, PID=%d\n", getpid());
    } else if (pid > 0) {
        // 父进程
        printf("我是父进程, 子PID=%d\n", pid);
    } else {
        perror("fork");
    }
    return 0;
}
```

`fork()` 一次调用，两次返回：父进程返回子进程 PID，子进程返回 0。

### 进程 PID

每个进程有唯一整数 PID。`getpid()` 取自己，`getppid()` 取父进程。

### 进程状态

- 运行：正在 CPU 上跑
- 等待：等事件（I/O、信号）
- 终止：结束但未被父进程回收（僵尸）

### wait 回收子进程

```c
wait(NULL);   // 父进程等子进程结束
```

不 wait 的话子进程变僵尸，占 PID 资源。

### 为什么多进程

- 进程间内存隔离，一个崩了不影响另一个
- 服务器可以一个进程服务一个客户
- 但进程重，创建切换开销大

## 核心重点
- 程序静态，进程动态
- fork 创建子进程，一次调用两次返回
- 子进程是父进程的副本
- wait 回收子进程
- POSIX API，Windows 用 WSL 实验

## 练习/任务
1. 在 WSL/Linux 下写 fork 程序，观察父子进程都打印
2. 用 getpid/getppid 打印进程号
3. 父进程 wait 子进程结束
4. （选做）理解为什么 fork 返回两次

## 今日刷题
- 今天系统编程为主，不安排算法题。

## 打卡清单
- [ ] 理解进程概念
- [ ] 会用 fork
- [ ] 知道 PID
- [ ] 理解进程隔离
