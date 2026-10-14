# Day 57 · 联合体与枚举

## 今日目标
- 理解 union 共享内存
- 掌握 enum 枚举类型
- 知道它们的典型用途

## 学习内容

### 联合体 union

union 让多个成员**共享同一块内存**，同一时间只能存一个成员：

```c
union Value {
    int   i;
    float f;
    char  s[20];
};
```

`sizeof(union Value)` 等于最大成员的大小（这里是 20）。所有成员从同一地址开始。

### 为什么用 union

节省内存 + 表达"二选一"。比如一个事件，可能是鼠标事件也可能是键盘事件，用 union 存两种数据，外加一个 tag 表示当前是哪种。

```c
typedef struct {
    int type;   // 1=鼠标, 2=键盘
    union {
        struct { int x, y; } mouse;
        struct { int key; } keyboard;
    } data;
} Event;
```

这就是"带标签的联合体"，C 标准库的 `struct sigaction` 等都这么设计。

### 枚举 enum

给整数起名字：

```c
enum Color { RED, GREEN, BLUE };
// RED=0, GREEN=1, BLUE=2

enum Color c = GREEN;
if (c == RED) { ... }
```

也可以指定值：

```c
enum Status { OK = 0, ERROR = 1, PENDING = 2 };
```

### 枚举 vs #define

```c
#define RED 0
#define GREEN 1
```

enum 更有类型，调试器能看到名字，推荐用 enum。

### 位标志枚举

枚举值用 2 的幂，可以位或组合：

```c
enum Flags {
    FLAG_READ  = 1 << 0,
    FLAG_WRITE = 1 << 1,
    FLAG_EXEC  = 1 << 2
};

int mode = FLAG_READ | FLAG_WRITE;
if (mode & FLAG_WRITE) { ... }
```

## 核心重点
- union 成员共享内存，同一时间只用一个
- 带标签联合体 = tag + union，表达"多态"数据
- enum 给整数起名字，比 #define 有类型
- 枚举值用 2 的幂可做位标志
- union 节省内存

## 练习/任务
1. 定义一个 union 存 int 或 float，写程序验证共享内存
2. 用 enum 表示星期几
3. 实现带标签的 Event 结构
4. （选做）用 union 判断机器大小端

## 今日刷题
- 今天学语法，复习之前题目。

## 打卡清单
- [ ] 理解 union 共享内存
- [ ] 会用 enum
- [ ] 知道带标签联合体
- [ ] 会用位标志枚举
