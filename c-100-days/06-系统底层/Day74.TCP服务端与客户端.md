# Day 74 · TCP服务端与客户端

## 今日目标
- 写一个最简单的 TCP 回显服务器
- 写对应的客户端
- 理解 connect/accept 流程

## 学习内容

### 服务器代码（POSIX/类 Unix 环境 Linux/macOS；Windows 可用 WSL）

```c
#include <stdio.h>
#include <string.h>
#include <unistd.h>
#include <sys/socket.h>
#include <netinet/in.h>

int main(void) {
    int server_fd = socket(AF_INET, SOCK_STREAM, 0);

    struct sockaddr_in addr;
    memset(&addr, 0, sizeof(addr));
    addr.sin_family = AF_INET;
    addr.sin_addr.s_addr = htonl(INADDR_ANY);
    addr.sin_port = htons(8080);

    bind(server_fd, (struct sockaddr*)&addr, sizeof(addr));
    listen(server_fd, 5);

    printf("服务器监听 8080...\n");
    int client_fd = accept(server_fd, NULL, NULL);

    char buf[1024] = {0};
    read(client_fd, buf, sizeof(buf));
    printf("收到: %s\n", buf);
    write(client_fd, "已收到\n", 6);

    close(client_fd);
    close(server_fd);
    return 0;
}
```

### 客户端代码

```c
#include <stdio.h>
#include <string.h>
#include <unistd.h>
#include <sys/socket.h>
#include <netinet/in.h>
#include <arpa/inet.h>

int main(void) {
    int fd = socket(AF_INET, SOCK_STREAM, 0);

    struct sockaddr_in addr;
    memset(&addr, 0, sizeof(addr));
    addr.sin_family = AF_INET;
    addr.sin_port = htons(8080);
    inet_pton(AF_INET, "127.0.0.1", &addr.sin_addr);

    connect(fd, (struct sockaddr*)&addr, sizeof(addr));
    write(fd, "你好服务器", 12);

    char buf[1024] = {0};
    read(fd, buf, sizeof(buf));
    printf("服务器说: %s\n", buf);

    close(fd);
    return 0;
}
```

### 编译运行（WSL/Linux）

```bash
gcc server.c -o server
gcc client.c -o client
./server &     # 后台跑服务器
./client       # 跑客户端
```

### 关键点

- `AF_INET` IPv4
- `SOCK_STREAM` TCP
- `INADDR_ANY` 本机所有网卡
- 端口号要 `htons` 转网络字节序

## 核心重点
- 服务器 socket-bind-listen-accept
- 客户端 socket-connect
- read/write 收发数据
- 端口要 htons
- 127.0.0.1 是本机回环

## 练习/任务
1. 把服务器和客户端代码敲一遍跑通
2. 服务器收到客户端消息后打印
3. 服务器回显客户端发来的内容
4. （选做）让服务器能接受多个客户端（循环 accept）

## 今日刷题
- 今天网络编程为主，不安排算法题。

## 打卡清单
- [ ] 会写 TCP 服务器
- [ ] 会写 TCP 客户端
- [ ] 理解 accept 与 connect
- [ ] 跑通本地回环通信
