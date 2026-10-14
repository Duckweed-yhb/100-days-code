# Day 63 · C进阶阶段练习

## 今日目标
- 综合运用位运算、宏、函数指针、文件 I/O
- 完成一个小工具：学生成绩文件管理
- 验收 C 进阶阶段

## 学习内容

### 项目：学生成绩管理（文件版）

把 Day13 的学生管理升级：数据存文件，重启不丢。

```c
#include <stdio.h>
#include <stdlib.h>
#include <string.h>

typedef struct {
    char name[20];
    int  score;
} Student;

#define MAX 100

int load(Student *class, const char *path) {
    FILE *fp = fopen(path, "rb");
    if (!fp) return 0;
    int n = fread(class, sizeof(Student), MAX, fp);
    fclose(fp);
    return n;
}

void save(Student *class, int n, const char *path) {
    FILE *fp = fopen(path, "wb");
    if (!fp) { perror("save"); return; }
    fwrite(class, sizeof(Student), n, fp);
    fclose(fp);
}

void list_all(Student *class, int n) {
    for (int i = 0; i < n; i++) {
        printf("%s %d\n", class[i].name, class[i].score);
    }
}

int main(void) {
    Student class[MAX];
    int n = load(class, "students.dat");
    printf("已加载 %d 条记录\n", n);

    // 添加新学生
    if (n < MAX) {
        printf("输入姓名 成绩: ");
        scanf("%19s %d", class[n].name, &class[n].score);
        n++;
    }

    list_all(class, n);
    save(class, n, "students.dat");
    return 0;
}
```

### 知识点串联

- 结构体：打包学生数据
- 文件 I/O：二进制读写
- 错误处理：fopen 检查
- 动态/静态数组：定长 MAX
- 字符串：strcpy、scanf %19s

### 阶段回顾

```
Day51-52 位运算
Day53-54 预处理、多文件
Day55-56 函数指针、回调
Day57-58 联合体、typedef
Day59-61 文件 I/O
Day62    错误处理
```

## 核心重点
- 文件让数据持久化
- 结构体 + 文件 = 简单数据库
- 工程化 = 多文件 + 错误处理 + 模块化
- 把前面学的串起来
- C 进阶到此结束，明天进入系统底层

## 练习/任务
1. 把上面的程序敲一遍跑通
2. 增加"按姓名查找"功能
3. 增加"删除指定学生"功能
4. （选做）加一个菜单循环：1 添加 2 列出 3 查找 4 退出

## 今日刷题
- 今天项目为主，复习之前题目。
- 可以重做：两数之和（1）、反转链表（206）。

## 打卡清单
- [ ] 完成学生成绩文件管理
- [ ] 理解二进制文件读写
- [ ] 综合运用 C 进阶特性
- [ ] C 进阶阶段达标
