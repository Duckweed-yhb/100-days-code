# Day 58 · typedef与类型定义

## 今日目标
- 熟练使用 typedef
- 区分 typedef 与 #define
- 用 typedef 让复杂类型可读

## 学习内容

### typedef 是什么

给已有类型起别名：

```c
typedef unsigned int uint;
uint a = 10;   // 等价 unsigned int a = 10;
```

### 给结构体起别名

```c
typedef struct {
    char name[20];
    int age;
} Student;

Student s = {"张三", 18};
```

不用每次写 `struct Student`。

### 给函数指针起别名

```c
typedef int (*CompareFunc)(const void *, const void *);

// 比 int (*CompareFunc)(const void*, const void*) 清晰
```

### typedef vs #define

```c
typedef char *String1;
#define  String2 char *;

String1 a, b;    // a, b 都是 char*
String2  c, d;    // c 是 char*, d 是 char！
```

`#define` 是文本替换，`typedef` 是真正的类型别名。**类型别名优先用 typedef**。

### 平台无关性

```c
typedef int int32_t;      // 32 位系统
typedef long long int64_t; // 64 位系统
```

标准库的 `size_t`、`int32_t` 都是 typedef，让代码跨平台。

### 常见标准类型

| 类型 | 含义 |
|---|---|
| `size_t` | sizeof 返回类型，无符号 |
| `ssize_t` | 有符号 size_t |
| `int32_t` | 精确 32 位 int |
| `int64_t` | 精确 64 位 int |
| `uint8_t` | 无符号 8 位 |

## 核心重点
- typedef 给类型起别名
- 结构体、函数指针都适合 typedef
- typedef 比 #define 安全（真正类型）
- 跨平台用 typedef 固定宽度
- size_t、int32_t 是标准别名

## 练习/任务
1. 用 typedef 定义 `typedef unsigned char byte`
2. 给链表节点起别名
3. 给函数指针类型起别名
4. （选做）思考为什么 size_t 是无符号

## 今日刷题
- 今天学语法，复习即可。

## 打卡清单
- [ ] 熟练使用 typedef
- [ ] 区分 typedef 与 #define
- [ ] 会给函数指针起别名
- [ ] 认识常用标准类型
