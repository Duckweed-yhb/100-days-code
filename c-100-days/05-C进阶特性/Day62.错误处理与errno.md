# Day 62 · 错误处理与errno

## 今日目标
- 理解 C 库函数如何报告错误
- 掌握 errno 与 perror/strerror
- 学会检查返回值

## 学习内容

### C 的错误报告方式

C 没有异常机制。库函数出错时：
- 返回特殊值（NULL、-1、EOF）
- 全局变量 `errno` 被设为错误码

### errno

```c
#include <errno.h>
extern int errno;
```

调用库函数后检查 errno：

```c
FILE *fp = fopen("notexist.txt", "r");
if (fp == NULL) {
    printf("错误码: %d\n", errno);
}
```

### perror

`perror("前缀")` 自动打印 errno 对应的文字：

```c
FILE *fp = fopen("notexist.txt", "r");
if (!fp) perror("fopen 失败");
// 输出: fopen 失败: No such file or directory
```

### strerror

把错误码转成字符串：

```c
#include <string.h>
printf("%s\n", strerror(ENOENT));
// No such file or directory
```

### 常见错误码

| 错误码 | 含义 |
|---|---|
| ENOENT | 文件不存在 |
| EACCES | 权限不够 |
| EINVAL | 参数无效 |
| ENOMEM | 内存不足 |
| EAGAIN | 资源暂时不可用 |

### 检查返回值的习惯

- `malloc` 返回 NULL → ENOMEM
- `fopen` 返回 NULL → 检查 errno
- `fread`/`fwrite` 返回值不等于 nmemb → 出错
- `scanf` 返回值不等于参数个数 → 输入格式不对

```c
int n;
if (scanf("%d", &n) != 1) {
    printf("输入不是整数\n");
    return 1;
}
```

### 错误处理不是可选的

初学者常忽略返回值，导致程序在错误情况下行为未定义。养成"每个系统调用都检查"的习惯。

## 核心重点
- C 用返回值 + errno 报告错误
- perror 一行打印错误原因
- strerror 把错误码转文字
- 每个库函数返回值都要检查
- 不检查返回值 = 埋雷

## 练习/任务
1. 故意打开不存在文件，用 perror 看错误
2. 用 strerror 打印 ENOENT、EACCES 描述
3. 写一个安全的 scanf 整数读取函数
4. （选做）故意 malloc 失败（申请超大内存）观察 errno

## 今日刷题
- 今天错误处理为主，不安排算法题。

## 打卡清单
- [ ] 理解 errno 机制
- [ ] 会用 perror/strerror
- [ ] 养成检查返回值习惯
- [ ] 认识常见错误码
