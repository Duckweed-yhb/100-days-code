# Day 12 · 二维数组与字符串

## 今日目标
- 掌握二维数组的声明、初始化与遍历
- 理解 C 语言字符串本质是以 `\0` 结尾的字符数组
- 学会用 `string.h` 常用函数处理字符串

## 学习内容

### 二维数组

```c
int matrix[3][4];                    // 3行4列
int a[2][3] = {{1,2,3}, {4,5,6}};   // 按行初始化
int b[][3] = {{1,2,3}, {4,5,6}};    // 行数可省，列数必须写
```

遍历：

```c
for (int i = 0; i < 2; i++) {
    for (int j = 0; j < 3; j++) {
        printf("%d ", a[i][j]);
    }
    printf("\n");
}
```

二维数组在内存中按行连续存放：先存第一行全部，再存第二行全部。

### 字符串就是字符数组

C 语言没有专门的字符串类型，字符串就是以 `'\0'`（ASCII 0）结尾的 `char` 数组：

```c
char s1[] = {'H', 'e', 'l', 'l', 'o', '\0'};   // 手动加 \0
char s2[] = "Hello";                            // 编译器自动加 \0
```

`"Hello"` 实际占 6 个字节：`H e l l o \0`。这个 `\0` 是字符串函数判断"字符串到哪结束"的依据，**漏掉它就是灾难**。

### 字符串输入输出

```c
char name[20];
scanf("%19s", name);              // 读一个单词（遇空格停）
printf("你好 %s\n", name);

// 读一整行（含空格）：
char line[100];
fgets(line, sizeof(line), stdin);  // 读一行，包括换行符
```

`fgets` 会把回车键的 `\n` 也存进去，必要时手动去掉。

### string.h 常用函数

```c
#include <string.h>

strlen(s);              // 字符串长度（不含 \0）
strcpy(dst, src);       // 复制
strcat(dst, src);       // 拼接
strcmp(a, b);           // 比较，相等返回0，a<b返回负数
strncpy(dst, src, n);   // 安全版复制，最多拷n个
strncat(dst, src, n);   // 安全版拼接
```

示例：

```c
char s1[20] = "Hello";
char s2[] = "World";

strcat(s1, " ");
strcat(s1, s2);
printf("%s\n", s1);          // Hello World
printf("%zu\n", strlen(s1)); // 11
```

### 为什么 strcpy 不安全

`strcpy(dst, src)` 不管 `dst` 多大，一直拷到遇到 `\0` 为止。如果 `src` 比 `dst` 长，就会溢出踩内存。生产代码推荐用 `strncpy` 并手动补 `\0`。

### 字符处理 ctype.h

```c
#include <ctype.h>

isdigit(c);   // 是否数字
isalpha(c);   // 是否字母
isupper(c);
islower(c);
toupper(c);   // 转大写
tolower(c);   // 转小写
```

## 核心重点
- 二维数组按行连续存储，行列下标都从 0 开始
- C 字符串是以 `\0` 结尾的 char 数组，没有这个结束符一切函数都乱套
- `scanf %s` 遇空格停，读整行用 `fgets`
- `string.h` 提供 strlen/strcpy/strcat/strcmp
- 用 `strncpy` 替代 `strcpy` 防止溢出

## 练习/任务
1. 求一个 3×3 矩阵的主对角线元素之和
2. 输入一个字符串，统计其中数字、字母、空格各有多少个
3. 输入两个字符串，不用 strcmp 自己实现比较（相等返回 0）
4. （选做）输入一个字符串，把其中小写字母全部转成大写（用 toupper）

## 打卡清单
- [ ] 能遍历二维数组
- [ ] 理解字符串以 \0 结尾的含义
- [ ] 熟练使用 string.h 常用函数
- [ ] 知道 fgets 与 scanf 的区别
