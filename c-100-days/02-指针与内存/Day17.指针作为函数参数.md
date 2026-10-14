# Day 17 · 指针作为函数参数

## 今日目标
- 用指针解决"函数内修改外部变量"的问题
- 实现真正的 swap
- 理解通过指针带回多个返回值

## 学习内容

### 回顾：为什么 Day09 的 swap 失败

因为 C 语言是值传递，函数拿到的是实参的副本。要让函数修改外部变量，必须把变量的地址传进去。

### 正确的 swap

```c
void swap(int *a, int *b) {
    int t = *a;
    *a = *b;
    *b = t;
}

int main(void) {
    int x = 1, y = 2;
    swap(&x, &y);          // 传地址！
    printf("%d %d\n", x, y);   // 2 1，真的交换了
    return 0;
}
```

原理：`a` 和 `b` 里存的是 `x` 和 `y` 的地址。`*a` 就是 `x` 本身，`*b` 就是 `y` 本身。在函数里 `*a = *b` 等价于 `x = y`。

### 带回多个返回值

C 函数只能 return 一个值，但可以通过指针参数"带回"多个结果：

```c
// 求商和余数，通过指针参数带回
void divmod(int a, int b, int *q, int *r) {
    *q = a / b;
    *r = a % b;
}

int main(void) {
    int quotient, remainder;
    divmod(17, 5, &quotient, &remainder);
    printf("商=%d 余=%d\n", quotient, remainder);   // 商=3 余=2
    return 0;
}
```

### 指针参数加 const

如果函数只想通过指针读数据、不修改，加 `const`：

```c
int sum_array(const int *a, int n) {
    int s = 0;
    for (int i = 0; i < n; i++) s += a[i];
    return s;
}
```

`const int *a` 表示"通过 a 不能修改所指内容"，既防止自己不小心改坏，也让调用者一眼看出这个参数是输入。

### 避免大结构体拷贝

Day13 说结构体默认值传递，整个拷一份。大结构体传指针更高效：

```c
typedef struct {
    char name[20];
    int score;
} Student;

void print_stu(const Student *s) {   // 只传 8 字节指针
    printf("%s %d\n", s->name, s->score);
}
```

注意：通过指针访问结构体成员用 `->` 而不是 `.`：`s->name` 等价于 `(*s).name`。

### 常见错误

- 忘记传地址：`swap(x, y)` 而不是 `swap(&x, &y)`，编译警告但不报错
- 函数内写成 `a, b` 而不是 `*a, *b`，改了指针本身而不是所指内容
- 传进去的是 NULL 还解引用，直接崩溃

## 核心重点
- 想让函数改外部变量，必须传地址
- `swap(int *a, int *b)` 通过解引用真的交换两个变量
- 指针参数可以带回多个结果
- 只读指针参数加 const
- 结构体指针用 `->` 访问成员

## 练习/任务
1. 实现正确的 `swap(int *a, int *b)` 并验证
2. 写函数 `void minmax(int *a, int n, int *min, int *max)` 同时返回最大最小值
3. 写函数 `void count(const char *s, int *letters, int *digits, int *others)` 统计字符类型
4. （选做）写一个函数 `void str_swap(char **a, char **b)` 交换两个字符串指针

## 打卡清单
- [ ] 理解指针参数为什么能改外部变量
- [ ] 实现真正的 swap
- [ ] 会用指针参数带回多个结果
- [ ] 掌握结构体指针用 -> 访问成员
