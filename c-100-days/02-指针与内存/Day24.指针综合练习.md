# Day 24 · 指针综合练习

## 今日目标
- 综合运用指针、数组、字符串、动态内存
- 完成几道指针专项小题
- 为明天的阶段项目热身

## 学习内容

### 复习：指针知识地图

```
指针 = 地址
 ├─ & 取地址，* 解引用
 ├─ 指针算术：p+1 指向下一个元素
 ├─ 数组名 = 首元素地址（常量）
 ├─ 函数参数：传值，要改外部变量就传地址
 ├─ 二级指针 T**：指向 T*
 ├─ 指针数组 T* arr[]：字符串数组
 ├─ 函数指针 返回类型 (*名)(参数)
 └─ 动态内存：malloc/calloc/realloc/free
```

### 例题 1：字符串反转

```c
void reverse(char *s) {
    char *left = s;
    char *right = s + strlen(s) - 1;
    while (left < right) {
        char t = *left;
        *left = *right;
        *right = t;
        left++;
        right--;
    }
}
```

双指针从两端向中间靠拢，交换字符。

### 例题 2：找数组最大值（指针版）

```c
int *find_max(const int *a, int n) {
    const int *p = a;
    const int *end = a + n;
    while (p < end) {
        if (*p > *a) {
            // 这里记录最大位置
        }
        p++;
    }
    // 返回指向最大值的指针
}
```

### 例题 3：动态字符串数组

```c
// 读入 n 个字符串，存在堆上
char **names = malloc(n * sizeof(char*));
for (int i = 0; i < n; i++) {
    names[i] = malloc(20);
    scanf("%19s", names[i]);
}
// 使用...
// 释放：先释放每个字符串，再释放指针数组
for (int i = 0; i < n; i++) free(names[i]);
free(names);
```

注意释放顺序：先里后外。

### 例题 4：用指针交换两个字符串

```c
void str_swap(char **a, char **b) {
    char *t = *a;
    *a = *b;
    *b = t;
}
```

要交换两个指针本身，就要传指针的地址（二级指针）。

### 常见思维误区

- 以为 `p++` 是地址加 1 字节——其实是按类型步进
- 以为函数传数组会拷贝——其实只传首地址
- 以为 malloc 后可以直接用——垃圾值要先初始化
- 以为 free 后指针就自动变 NULL——不会，必须手动置

## 核心重点
- 指针三剑客：&、*、->
- 双指针技巧：两端向中间、快慢指针
- 动态二维数组：先分配指针数组，再逐行分配
- free 顺序：先 free 内层，再 free 外层
- 指针题画图辅助理解

## 练习/任务
1. 实现 `void reverse(char *s)` 反转字符串
2. 实现 `int *max_element(int *a, int n)` 返回指向最大值的指针
3. 动态分配一个 n×m 的 int 矩阵，赋值后打印
4. （选做）写一个函数 `int count_words(const char *s)` 统计字符串中单词个数（空格分隔）

## 打卡清单
- [ ] 能熟练使用指针遍历数组和字符串
- [ ] 会用双指针解决问题
- [ ] 能正确分配和释放动态二维数组
- [ ] 已完成指针阶段所有练习
