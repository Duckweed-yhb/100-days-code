# Day 65 · 静态库与动态库

## 今日目标
- 理解库是什么
- 学会制作和使用静态库
- 了解动态库与静态库区别

## 学习内容

### 库是什么

库就是一堆编译好的 .o 文件打包，别人可以链接使用，不用给源码。

### 静态库

编译时把库代码**完整拷贝**进可执行文件。Windows 是 `.lib`，Linux 是 `.a`。

制作静态库（Linux/WSL）：

```bash
gcc -c utils.c -o utils.o
ar rcs libutils.a utils.o
```

使用：

```bash
gcc main.c -L. -lutils -o app
```

- `-L.` 在当前目录找库
- `-lutils` 链接 libutils.a

### 动态库

运行时才加载到内存，多个程序共享同一份。Windows 是 `.dll`，Linux 是 `.so`。

```bash
gcc -fPIC -c utils.c -o utils.o
gcc -shared utils.o -o libutils.so
gcc main.c -L. -lutils -o app
```

### 静态 vs 动态

| 方面 | 静态库 | 动态库 |
|---|---|---|
| 链接时机 | 编译时 | 运行时 |
| 可执行文件大小 | 大 | 小 |
| 运行时依赖 | 无 | 需要 .dll/.so |
| 库升级 | 要重新链接 | 换库即可 |
| 内存共享 | 不共享 | 多进程共享 |

### 标准库就是库

你用的 printf、malloc 都在 libc 里。程序运行时动态链接 libc。

## 核心重点
- 库 = 编译好的 .o 打包
- 静态库编译时拷进可执行文件
- 动态库运行时共享
- Windows: .lib/.dll，Linux: .a/.so
- -L 找路径，-l 指定库名

## 练习/任务
1. 写一个 utils.c，编译成静态库
2. 在 main.c 中链接静态库使用
3. 观察可执行文件大小
4. （选做）尝试做一个动态库

## 今日刷题
- 今天工程为主，不安排算法题。

## 打卡清单
- [ ] 理解库的概念
- [ ] 会做静态库
- [ ] 区分静态与动态
- [ ] 知道 -L 和 -l 用法
