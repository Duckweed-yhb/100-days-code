# Day 55 · 函数指针深入

## 今日目标
- 复习函数指针声明
- 理解回调函数机制
- 用函数指针实现策略模式

## 学习内容

### 函数指针声明回顾

```c
int (*fp)(int, int);
```

读作：fp 是指针，指向"参数两个 int、返回 int"的函数。

### typedef 让它可读

```c
typedef int (*BinaryOp)(int, int);

BinaryOp add = ...;   // 比 int (*)(int,int) 清爽
```

### 回调函数

把函数 A 传给函数 B，B 在合适时机调用 A。A 就是回调。

```c
typedef void (*Callback)(int);

void process(int *data, int n, Callback cb) {
    for (int i = 0; i < n; i++) {
        cb(data[i]);
    }
}

void print_int(int x) { printf("%d ", x); }
void square(int x)    { printf("%d ", x*x); }

int main(void) {
    int a[] = {1, 2, 3, 4};
    process(a, 4, print_int);   // 打印 1 2 3 4
    process(a, 4, square);     // 打印 1 4 9 16
    return 0;
}
```

process 不关心具体做什么，行为由传入的回调决定。

### qsort 回调

Day19 见过：

```c
int cmp(const void *a, const void *b) {
    return *(int*)a - *(int*)b;
}
qsort(arr, n, sizeof(int), cmp);
```

qsort 不知道元素类型，比较逻辑由 cmp 回调提供。

### 函数指针数组

```c
typedef void (*MenuAction)(void);

void open(void)  { printf("打开\n"); }
void save(void)  { printf("保存\n"); }
void quit(void)  { printf("退出\n"); }

MenuAction menu[] = {open, save, quit};
// 用户选 0/1/2，直接调用 menu[choice]();
```

这就是简单的菜单分发。

### 为什么 C 程序员要懂函数指针

- 标准库大量用（qsort、bsearch、信号处理）
- 事件驱动编程（GUI 按钮回调）
- 实现面向对象的"策略"和"虚函数表"

## 核心重点
- 函数指针存函数地址
- typedef 让声明可读
- 回调 = 把函数当参数传
- qsort 是最经典的回调应用
- 函数指针数组实现分发

## 练习/任务
1. 写一个 foreach 函数，对数组每个元素调用回调
2. 用 qsort 对字符串数组排序
3. 用函数指针数组实现菜单
4. （选做）写一个计算器，根据运算符字符串调用对应函数

## 今日刷题
- **排序数组**（LeetCode 第 912 题）：用 qsort 一行搞定。
- 今天重点在理解回调，算法题以复习为主。

## 打卡清单
- [ ] 熟练声明函数指针
- [ ] 理解回调机制
- [ ] 会用 qsort
- [ ] 能写函数指针数组
