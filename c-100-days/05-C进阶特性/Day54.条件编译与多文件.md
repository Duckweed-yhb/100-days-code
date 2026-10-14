# Day 54 · 条件编译与多文件

## 今日目标
- 掌握 #ifdef #ifndef #if 条件编译
- 学会多文件项目组织
- 理解 extern 与头文件声明

## 学习内容

### 条件编译

根据条件决定哪些代码参与编译：

```c
#define DEBUG

int main(void) {
#ifdef DEBUG
    printf("调试模式\n");
#endif
    // 正常代码
    return 0;
}
```

`gcc -DDEBUG` 可以在命令行定义宏，不用改代码。

### 常用条件编译

```c
#ifdef DEBUG
    // 调试代码
#endif

#ifndef DEBUG
    // 非调试代码
#endif

#if VERSION >= 2
    // 新版本功能
#endif
```

### 多文件项目

当项目变大，不能把所有代码塞一个 .c 里。标准组织方式：

```
project/
 ├─ main.c       // 主程序
 ├─ student.c    // 学生相关函数实现
 ├─ student.h    // 学生相关函数声明
 └─ utils.c      // 工具函数
```

### 头文件放什么

头文件 `.h` 放**声明**：
- 函数原型
- 宏定义
- 类型定义（struct、typedef）
- 全局变量 extern 声明

```c
// student.h
#ifndef STUDENT_H
#define STUDENT_H

typedef struct {
    char name[20];
    int score;
} Student;

void print_student(const Student *s);
extern int g_count;

#endif
```

### 源文件放什么

`.c` 放**实现**：

```c
// student.c
#include "student.h"
#include <stdio.h>

int g_count = 0;

void print_student(const Student *s) {
    printf("%s %d\n", s->name, s->score);
    g_count++;
}
```

### 使用

```c
// main.c
#include <stdio.h>
#include "student.h"

int main(void) {
    Student s = {"张三", 90};
    print_student(&s);
    return 0;
}
```

编译：

```powershell
gcc main.c student.c -o app
```

### extern

`extern int g_count;` 告诉编译器"这个变量在别的 .c 文件里定义了"，不要在这里分配内存。真正定义在 student.c 里。

## 核心重点
- 条件编译按开关裁剪代码
- 头文件放声明，源文件放实现
- 头文件卫士防止重复包含
- extern 声明跨文件全局变量
- 多文件分别编译再链接

## 练习/任务
1. 写一个多文件项目：student.h / student.c / main.c
2. 加 DEBUG 宏控制是否打印调试信息
3. 用 `gcc main.c student.c` 编译运行
4. （选做）加一个 utils.c 放通用函数

## 今日刷题
- 今天学工程结构，不安排算法题。

## 打卡清单
- [ ] 会用条件编译
- [ ] 理解头文件与源文件分工
- [ ] 会用 extern 跨文件引用
- [ ] 能编译多文件项目
