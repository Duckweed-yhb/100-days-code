# Day 13 · 结构体与基础综合练习

## 今日目标
- 掌握结构体的定义、声明与成员访问
- 能用结构体把相关数据打包在一起
- 综合运用前 12 天知识完成一个小练习

## 学习内容

### 为什么需要结构体

前面我们用数组存"一群同类型数据"。但现实中一个学生有姓名（字符串）、年龄（int）、成绩（float），类型不同，没法放一个数组。结构体就是把不同类型的数据打包成一个自定义类型。

### 定义结构体

```c
struct Student {
    char name[20];
    int  age;
    float score;
};
```

这只是定义了一个"模板"，还没分配内存。接下来声明变量：

```c
struct Student s1;                        // 声明一个变量
struct Student s2 = {"张三", 18, 92.5f};   // 声明同时初始化
```

### 访问成员

用 `.` 运算符：

```c
s1.age = 19;
s1.score = 88.0f;
strcpy(s1.name, "李四");

printf("%s 今年 %d 岁, 成绩 %.1f\n", s1.name, s1.age, s1.score);
```

### 结构体数组

```c
struct Student class[3] = {
    {"张三", 18, 92.5f},
    {"李四", 19, 87.0f},
    {"王五", 18, 95.5f}
};

for (int i = 0; i < 3; i++) {
    printf("%s: %.1f\n", class[i].name, class[i].score);
}
```

### 结构体做函数参数

结构体可以整体传给函数，但默认是值传递（整个拷贝一份）。大结构体建议传指针（Day15 学）。

```c
void print_student(struct Student s) {
    printf("%s %d %.1f\n", s.name, s.age, s.score);
}
```

### typedef 给结构体起别名

```c
typedef struct {
    char name[20];
    int  age;
} Student;

Student s = {"张三", 18};   // 不用再写 struct 关键字
```

### 综合小练习：学生成绩统计

把前 12 天的知识串起来：

```c
#include <stdio.h>
#include <string.h>

typedef struct {
    char name[20];
    int  score;
} Student;

int main(void) {
    Student class[5] = {
        {"张三", 85}, {"李四", 92}, {"王五", 78},
        {"赵六", 90}, {"钱七", 66}
    };
    int n = 5;

    int sum = 0, max_idx = 0;
    for (int i = 0; i < n; i++) {
        sum += class[i].score;
        if (class[i].score > class[max_idx].score) {
            max_idx = i;
        }
    }

    printf("平均分: %.1f\n", (double)sum / n);
    printf("最高分: %s %d\n", class[max_idx].name, class[max_idx].score);

    for (int i = 0; i < n; i++) {
        if (class[i].score >= 60) {
            printf("%s 及格\n", class[i].name);
        }
    }
    return 0;
}
```

## 核心重点
- 结构体把不同类型的数据打包成自定义类型
- 用 `.` 访问成员，字符串成员用 `strcpy` 赋值
- 结构体数组是管理"一组记录"的利器
- `typedef struct {...} 别名;` 省去每次写 struct
- 结构体默认值传递，大对象传指针更高效（预告）

## 练习/任务
1. 定义一个 `struct Point { int x; int y; }`，计算两点之间的距离（用 sqrt，需 `#include <math.h>`）
2. 定义一个 `struct Book { char title[50]; char author[20]; float price; }`，存 3 本书，按价格从高到低输出
3. 输入 5 个学生的姓名和成绩，找出并打印成绩最高的学生信息
4. （选做）给结构体加一个 `char id[15]` 学号字段，练习 `strcpy` 赋值

## 打卡清单
- [ ] 能定义和使用结构体
- [ ] 会用结构体数组管理一组记录
- [ ] 理解 typedef 的作用
- [ ] 已完成学生成绩统计小练习
