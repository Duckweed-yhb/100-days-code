# Day 16 · 字符指针与字符串操作

## 今日目标
- 理解字符数组与字符指针的区别
- 掌握用指针操作字符串
- 手写 string.h 中几个经典函数

## 学习内容

### 两种定义字符串的方式

```c
char s1[] = "Hello";     // 字符数组：内容在栈上，可修改
const char *s2 = "Hello";// 字符指针：指向字符串常量，不可修改
```

区别很关键：
- `s1[0] = 'h'` 合法（数组内容可改）
- `s2[0] = 'h'` **未定义行为**！字符串常量通常放在只读内存段，改了会崩溃

### 字符指针遍历字符串

```c
const char *s = "Hello";
while (*s != '\0') {
    putchar(*s);
    s++;
}
putchar('\n');
```

利用"字符串以 `\0` 结尾"这个特性，循环不需要长度。

### 手写 strlen

```c
size_t my_strlen(const char *s) {
    const char *p = s;
    while (*p != '\0') p++;
    return p - s;
}
```

### 手写 strcpy

```c
char *my_strcpy(char *dst, const char *src) {
    char *save = dst;
    while ((*dst++ = *src++) != '\0');
    return save;
}
```

这个 `while ((*dst++ = *src++));` 是 C 经典写法：把 `*src` 赋给 `*dst`，两个指针都后移，直到赋到 `\0` 为止（赋值表达式的值就是被赋的值，`\0` 就是 0，循环结束）。

### 手写 strcat

```c
char *my_strcat(char *dst, const char *src) {
    char *save = dst;
    while (*dst) dst++;          // 先走到 dst 末尾
    while ((*dst++ = *src++));   // 再拼接
    return save;
}
```

### 字符串比较 strcmp

```c
int my_strcmp(const char *a, const char *b) {
    while (*a && (*a == *b)) {
        a++;
        b++;
    }
    return (unsigned char)*a - (unsigned char)*b;
}
```

返回值：相等为 0，a < b 为负数，a > b 为正数。

### 字符串字面值是只读的

```c
char *s = "hello";       // 不推荐，应该写 const char *s
s[0] = 'H';              // 未定义行为，可能崩溃
```

好习惯：永远用 `const char *` 指向字符串字面值。

## 核心重点
- 字符数组可改，字符指针指向的字符串常量不可改
- 字符串函数的核心是利用 `\0` 结束符
- `*dst++ = *src++` 是 C 经典字符串拷贝写法
- 字符指针参数加 `const` 表示不修改内容
- 用 const char* 指向字符串字面值

## 练习/任务
1. 手写 `my_strlen`，与库函数 `strlen` 对比结果
2. 手写 `my_strcpy`，验证拷贝后内容正确
3. 写一个函数 `int count_char(const char *s, char c)` 统计字符 c 出现次数
4. （选做）写一个函数 `void reverse(char *s)` 反转字符串（提示：双指针从两端向中间）

## 打卡清单
- [ ] 区分字符数组与字符指针
- [ ] 能手写 strlen/strcpy
- [ ] 理解 \0 结束符在字符串函数中的作用
- [ ] 养成用 const 保护只读数据的习惯
