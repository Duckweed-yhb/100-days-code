# Day 83 · 网络编程概念与socket

## 今日目标
- 理解 TCP/IP、客户端/服务器模型
- 认识 POSIX socket 基本 API
- 了解本课程只讲概念，不深入实现

## 学习内容

### 网络分层（概念）

应用层（HTTP、FTP）→ 传输层（TCP/UDP）→ 网络层（IP）→ 链路层。我们写网络程序主要面对应用层，用 TCP/UDP socket。

### C/S 模型

- **服务器**：绑定端口、监听、接受连接；
- **客户端**：连接服务器、发请求、收响应。

### TCP vs UDP

| 对比 | TCP | UDP |
|---|---|---|
| 连接 | 面向连接 | 无连接 |
| 可靠 | 保证到达、有序 | 不保证 |
| 速度 | 较慢 | 快 |
| 场景 | 网页、文件、聊天 | 视频直播、DNS |

### POSIX socket API（概念）

C++ 标准库本身**没有**网络库，工业界用 POSIX socket（Linux/macOS）或 Winsock（Windows）。核心调用：

```cpp
// 服务器
int sock = socket(AF_INET, SOCK_STREAM, 0);
bind(sock, ...);
listen(sock, 10);
int conn = accept(sock, ...);
read(conn, buf, n);
write(conn, buf, n);
close(conn);

// 客户端
int sock = socket(AF_INET, SOCK_STREAM, 0);
connect(sock, ...);
write(sock, "hello", 5);
read(sock, buf, n);
close(sock);
```

今天只需要认识这些名字，不要求能写出来。

### 现代替代

工程上常用：

- libcurl（HTTP 客户端）；
- Boost.Asio（异步网络库）；
- 自己封装 socket。

### 环境说明

Windows 上用 Winsock，需要 `#include <winsock2.h>` 并链接 ws2_32.lib。Linux 上直接 `<sys/socket.h>`。本阶段只讲概念，不要求在本机编译网络程序。

## 核心重点
- C++ 标准库无网络库，用 POSIX socket
- TCP 面向连接可靠，UDP 无连接快
- 服务器：socket/bind/listen/accept
- 客户端：socket/connect
- 今天只讲概念

## 练习/任务
1. 查资料了解 HTTP 协议基本流程
2. 用浏览器访问一个网站，F12 看请求/响应
3. （选做）了解 curl 命令：`curl https://example.com`
4. （选做）查资料了解 TCP 三次握手

## 今日刷题

- **LeetCode 127. 单词接龙**：BFS。
- **LeetCode 200. 岛屿数量**。
- **LeetCode 994. 腐烂的橘子**。

## 打卡清单
- [ ] 已理解 C/S 模型
- [ ] 已分清 TCP 与 UDP
- [ ] 已认识 socket 基本 API
- [ ] 已知道 C++ 标准库无网络库
