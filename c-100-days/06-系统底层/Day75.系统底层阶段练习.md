# Day 75 · 系统底层阶段练习

## 今日目标
- 综合运用进程、线程、同步、socket
- 写一个多线程回声服务器
- 验收系统底层阶段

## 学习内容

### 项目：多线程回声服务器

主线程 accept，每来一个客户端开一个线程处理：

```c
#include <stdio.h>
#include <string.h>
#include <unistd.h>
#include <pthread.h>
#include <sys/socket.h>
#include <netinet/in.h>

void *handle_client(void *arg) {
    int fd = *(int*)arg;
    free(arg);
    char buf[1024];
    while (1) {
        int n = read(fd, buf, sizeof(buf));
        if (n <= 0) break;
        write(fd, buf, n);   // 回声
    }
    close(fd);
    return NULL;
}

int main(void) {
    int server_fd = socket(AF_INET, SOCK_STREAM, 0);
    struct sockaddr_in addr = {0};
    addr.sin_family = AF_INET;
    addr.sin_addr.s_addr = htonl(INADDR_ANY);
    addr.sin_port = htons(8080);

    bind(server_fd, (struct sockaddr*)&addr, sizeof(addr));
    listen(server_fd, 10);

    while (1) {
        int *client_fd = malloc(sizeof(int));
        *client_fd = accept(server_fd, NULL, NULL);
        pthread_t tid;
        pthread_create(&tid, NULL, handle_client, client_fd);
        pthread_detach(tid);
    }
    return 0;
}
```

### 知识点串联

- socket：网络通信
- pthread：多线程并发
- malloc：客户端 fd 传给线程
- pthread_detach：线程结束自动回收

### 阶段回顾

```
Day64-65 编译链接、库
Day66-67 内存模型、栈堆
Day68-69 进程、IPC
Day70-72 线程、互斥锁、条件变量
Day73-74 socket
Day75     综合
```

### 下一步

系统底层到此结束。接下来 Stage 07 回到算法进阶：动态规划、贪心、图算法。

## 核心重点
- 多线程服务器：主线程 accept，子线程处理
- 每个客户端一个线程
- pthread_detach 自动回收
- 系统编程 = 进程 + 线程 + 网络
- POSIX API 在 Windows 用 WSL 学

## 练习/任务
1. 跑通多线程回声服务器
2. 用 telnet 或 nc 连接测试
3. 给服务器加一个全局连接数计数器（用互斥锁保护）
4. （选做）让服务器收到 "quit" 时关闭客户端连接

## 今日刷题
- 今天项目为主，复习之前算法题。
- 可以重做：岛屿数量（200）、合并区间（56）。

## 打卡清单
- [ ] 跑通多线程服务器
- [ ] 理解主线程/工作线程分工
- [ ] 综合运用系统编程
- [ ] 系统底层阶段达标
