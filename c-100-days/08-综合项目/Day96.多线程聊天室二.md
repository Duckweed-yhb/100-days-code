# Day 96 · 多线程聊天室二

## 今日目标
- 完成客户端
- 实现用户名
- 调试并发问题

## 学习内容

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
    struct sockaddr_in addr = {0};
    addr.sin_family = AF_INET;
    addr.sin_port = htons(8080);
    inet_pton(AF_INET, "127.0.0.1", &addr.sin_addr);
    connect(fd, (struct sockaddr*)&addr, sizeof(addr));

    char buf[1024];
    while (fgets(buf, sizeof(buf), stdin)) {
        write(fd, buf, strlen(buf));
    }
    close(fd);
    return 0;
}
```

### 运行

```bash
./server &
./client &
./client
# 多个客户端各自输入，互相看到消息
```

### 调试要点

- 加锁保护共享列表
- 客户端断开要从列表移除
- read 返回 0 表示对端关闭
- 编译加 -pthread

### 可能遇到的问题

- 段错误：忘记加锁、列表越界
- 收不到消息：广播时跳过了发送者自己
- 断开后崩溃：没正确移除 fd

## 核心重点
- 客户端简单：连接、读写
- 服务器复杂：并发、广播、清理
- 并发程序要考虑竞态
- 多线程调试比单线程难
- 跑通就是胜利

## 练习/任务
1. 完成客户端
2. 开两个客户端互相聊天
3. 处理客户端断开
4. （选做）加用户名前缀

## 今日刷题
- 今天项目为主，不安排算法题。

## 打卡清单
- [ ] 跑通多客户端聊天
- [ ] 理解并发问题
- [ ] 会处理断开
- [ ] 综合 socket + pthread
