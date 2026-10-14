# Day 99 · Makefile构建项目

## 今日目标
- 理解为什么需要 Make
- 会写简单 Makefile
- 理解增量编译

## 学习内容

### 为什么需要 Make

多文件项目手动 `gcc a.c b.c c.c` 每次全量编译。改一个文件全部重编，慢。Make 只编译改过的。

### 最简单 Makefile

```make
app: main.o student.o
	gcc main.o student.o -o app

main.o: main.c student.h
	gcc -c main.c -o main.o

student.o: student.c student.h
	gcc -c student.c -o student.o

clean:
	rm -f *.o app
```

注意：命令行前面必须是 **Tab**，不是空格。

### 运行

```bash
make          # 构建
make clean    # 清理
```

### 变量与规则

```make
CC = gcc
CFLAGS = -Wall -g

app: main.o student.o
	$(CC) main.o student.o -o app

%.o: %.c
	$(CC) $(CFLAGS) -c $< -o $@
```

`%.o: %.c` 模式规则：任何 .o 依赖同名 .c。`$<` 是依赖，`$@` 是目标。

### 增量编译原理

Make 比较文件修改时间，只重建比依赖旧的目标。改了 student.c，只重编 student.o 再链接。

## 核心重点
- Make 自动化构建
- Makefile 规则：目标:依赖 + 命令
- 命令前 Tab
- 增量编译省时
- 多文件项目必备

## 练习/任务
1. 给学生管理系统写 Makefile
2. 改一个 .c 文件观察只重编它
3. 写 clean 目标
4. （选做）加 debug/release 目标

## 今日刷题
- 今天工程为主，不安排算法题。

## 打卡清单
- [ ] 会写简单 Makefile
- [ ] 理解增量编译
- [ ] 会用 make clean
- [ ] 项目构建自动化
