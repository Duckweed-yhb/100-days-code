# Day 11 · 数组与字符串

## 今日目标
- 掌握一维、二维数组的定义与遍历
- 理解 C 风格字符串的本质
- 初步认识 C++ 标准库 `std::string`

## 学习内容

### 一维数组

```cpp
int arr[5] = {1, 2, 3, 4, 5};
for (int i = 0; i < 5; ++i) {
    std::cout << arr[i] << ' ';
}
```

- 数组下标从 0 开始；
- 定义时如果给了初始化列表，长度可省略：`int arr[] = {1,2,3};`；
- 越界访问（`arr[100]`）是未定义行为，程序可能崩也可能莫名其妙出错。

### 二维数组

```cpp
int mat[2][3] = {
    {1, 2, 3},
    {4, 5, 6}
};
for (int i = 0; i < 2; ++i)
    for (int j = 0; j < 3; ++j)
        std::cout << mat[i][j] << ' ';
```

### 数组与指针

数组名在大多数表达式里会退化为指向首元素的指针：

```cpp
int arr[5] = {10, 20, 30, 40, 50};
int* p = arr;       // p 指向 arr[0]
p[1] = 99;          // 等价于 arr[1] = 99
*(p + 2) = 88;      // 等价于 arr[2] = 88
```

### C 风格字符串

C++ 兼容 C 语言的字符串：以 `'\0'` 结尾的字符数组。

```cpp
char name[] = "hello";    // 实际占 6 个 char：h e l l o \0
std::cout << name;        // 自动输出到 \0 为止
```

常用 C 字符串函数（在 `<cstring>` 中）：

```cpp
#include <cstring>

char a[20] = "hello";
char b[20] = "world";
strlen(a);            // 5
strcpy(a, b);         // 把 b 复制到 a
strcmp(a, b);         // 比较，相等返回 0
strcat(a, "!");       // 拼接
```

这些函数不安全（容易越界），工程里更推荐用 C++ 的 `std::string`。

### std::string 初体验

```cpp
#include <string>
#include <iostream>

int main() {
    std::string s = "hello";
    s += " world";              // 拼接
    std::cout << s.size();      // 11
    std::cout << s[0];          // 'h'
}
```

`std::string` 会自动管理内存、支持 `+` 拼接、`==` 比较，比 C 风格字符串安全得多。Day41 会专门讲。

## 核心重点
- 数组下标从 0 开始，越界是未定义行为
- 数组名会退化为指向首元素的指针
- C 风格字符串是 `'\0'` 结尾的 char 数组
- 工程中优先用 `std::string`，别裸用 `strcpy`
- 二维数组 `mat[i][j]` 按行存储

## 练习/任务
1. 定义一个含 10 个 int 的数组，求最大值、最小值、平均值
2. 定义 3x3 的二维数组，打印它的转置矩阵
3. 用 C 风格字符串读取用户输入的名字，输出 `Hello, <名字>!`
4. （选做）把上面的字符串例子改用 `std::string` 实现

## 打卡清单
- [ ] 已会遍历一维、二维数组
- [ ] 已理解数组名退化为指针
- [ ] 已知道 C 风格字符串以 \0 结尾
- [ ] 已会用 std::string 做基本拼接与访问
