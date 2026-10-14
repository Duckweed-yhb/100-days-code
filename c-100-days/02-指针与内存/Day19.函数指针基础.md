# Day 19 · 函数指针基础

## 今日目标
- 理解函数在内存中也有地址
- 学会声明和使用函数指针
- 初步体会回调函数的思想

## 学习内容

### 函数名就是入口地址

函数编译后会变成一段机器码，存在代码段。函数名就是这段机器码的起始地址。

```c
int add(int a, int b) { return a + b; }

int main(void) {
    printf("%p\n", (void*)add);   // 打印函数地址
    return 0;
}
```

### 声明函数指针

```c
int (*fp)(int, int);
```

读法：`fp` 是一个指针，指向"参数为两个 int、返回 int"的函数。

注意括号：`int *fp(int, int)` 是"返回 int* 的函数声明"，不是指针。括号让 `*fp` 先结合，表示 fp 是指针。

### 赋值与调用

```c
int add(int a, int b) { return a + b; }
int sub(int a, int b) { return a - b; }

int main(void) {
    int (*fp)(int, int);

    fp = add;                    // 指向 add
    printf("%d\n", fp(3, 5));    // 8

    fp = sub;                    // 改指向 sub
    printf("%d\n", fp(3, 5));    // -2
    return 0;
}
```

### 作为函数参数

函数指针最大的用处：把"函数"本身当参数传给另一个函数。

```c
int apply(int x, int y, int (*op)(int, int)) {
    return op(x, y);
}

int main(void) {
    printf("%d\n", apply(3, 5, add));   // 8
    printf("%d\n", apply(3, 5, sub));   // -2
    return 0;
}
```

`apply` 不关心具体做什么运算，运算逻辑由调用者传入的函数决定。这就是"回调"思想。

### typedef 简化

```c
typedef int (*BinaryOp)(int, int);

int apply(int x, int y, BinaryOp op) {
    return op(x, y);
}
```

### 经典案例：qsort

C 标准库的快速排序 `qsort` 就是用函数指针实现"通用排序"：

```c
#include <stdlib.h>

int cmp_int(const void *a, const void *b) {
    return *(const int*)a - *(const int*)b;
}

int main(void) {
    int arr[] = {5, 2, 8, 1, 9};
    int n = 5;
    qsort(arr, n, sizeof(int), cmp_int);
    // arr 现在是 {1, 2, 5, 8, 9}
    return 0;
}
```

`qsort` 不知道元素类型，靠调用者传入比较函数。这就是函数指针的威力。

## 核心重点
- 函数名是函数代码段的入口地址
- 函数指针声明：`返回类型 (*名字)(参数类型)`
- 函数指针可以当参数传递，实现回调
- 标准库 qsort 就是函数指针的经典应用
- 用 typedef 让函数指针类型更易读

## 练习/任务
1. 声明一个函数指针，分别指向 add 和 mul，调用并打印结果
2. 写一个 `apply_op(int a, int b, int (*op)(int,int))`，传入不同运算函数
3. 用 qsort 对一个 int 数组升序排序
4. （选做）写一个对字符串数组排序的比较函数，传给 qsort

## 打卡清单
- [ ] 能正确声明函数指针
- [ ] 理解函数名就是地址
- [ ] 会用函数指针做参数实现回调
- [ ] 会用 qsort 排序
