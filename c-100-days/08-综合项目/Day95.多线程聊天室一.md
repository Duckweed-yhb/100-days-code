# Day 95 · 多线程聊天室一

## 今日目标
- 设计多线程聊天室架构
- 实现服务器端广播
- 理解客户端列表管理

## 学习内容

### 架构

- 服务器：主线程 accept，每个客户端一个线程
- 收到某客户端消息，广播给所有其他客户端
- 需要互斥锁保护客户端列表

POSIX/类 Unix 环境（Linux/macOS；Windows 可用 WSL）。

### 客户端列表

```c
#define MAX_CLIENTS 10
int clients[MAX_CLIENTS];
int client_count = 0;
pthread_mutex_t clients_lock = PTHREAD_MUTEX_INITIALIZER;

void add_client(int fd) {
    pthread_mutex_lock(&clients_lock);
    clients[client_count++] = fd;
    pthread_mutex_unlock(&clients_lock);
}

void broadcast(int from_fd, const char *msg) {
    pthread_mutex_lock(&clients_lock);
    for (int i = 0; i < client_count; i++) {
        if (clients[i] != from_fd) {
            write(clients[i], msg, strlen(msg));
        }
    }
    pthread_mutex_unlock(&clients_lock);
}
```

### 客户端处理线程

```c
void *handle_client(void *arg) {
    int fd = *(int*)arg;
    free(arg);
    char buf[1024];
    while (1) {
        int n = read(fd, buf, sizeof(buf)-1);
        if (n <= 0) break;
        buf[n] = '\0';
        broadcast(fd, buf);
    }
    // 移除 fd...
    close(fd);
    return NULL;
}
```

## 核心重点
- 每客户端一线程
- 互斥锁保护客户端列表
- 广播遍历所有连接
- 共享数据必须加锁
- socket + pthread 综合

## 练习/任务
1. 实现客户端列表
2. 实现广播
3. 写服务器 accept 循环
4. （选做）写简单客户端

## 今日刷题
- 今天项目为主，不安排算法题。

## 打卡清单
- [ ] 理解聊天室架构
- [ ] 会用互斥锁保护列表
- [ ] 实现广播
- [ ] socket 与 pthread 综合
