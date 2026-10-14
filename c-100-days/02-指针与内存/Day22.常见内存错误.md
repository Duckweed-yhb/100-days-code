# Day 22 · 常见内存错误

## 今日目标
- 认识 C 程序中最常见的内存错误类型
- 学会写出"安全"的指针代码
- 建立对内存错误的敏感度

## 学习内容

### 1. 野指针

指针指向了不确定的地址：

```c
int *p;          // 未初始化，指向随机地址
*p = 42;         // 未定义行为，可能崩溃
```

解决：声明时初始化，要么 `= NULL`，要么 `= &x`。

### 2. 空指针解引用

```c
int *p = NULL;
*p = 10;         // 崩溃！段错误
```

每次用指针前判断：`if (p != NULL) ...`。

### 3. 重复释放

```c
free(p);
free(p);         // 未定义行为，通常崩溃
```

### 4. 释放后使用（Use-After-Free）

```c
int *p = malloc(sizeof(int));
*p = 42;
free(p);
printf("%d\n", *p);   // 内存已归还，还在读！
```

这块内存可能已经被系统分给别的地方了，读出来是垃圾值，写回去会破坏别人的数据。解决：free 后立刻 `p = NULL;`。

### 5. 缓冲区溢出

```c
char buf[10];
strcpy(buf, "这是一段非常非常长的字符串");   // 超过10字节！
```

会踩坏栈上相邻数据，可能被利用做安全攻击。用 `strncpy`、`fgets` 限制长度。

### 6. 内存泄漏

```c
void func(void) {
    int *p = malloc(100 * sizeof(int));
    // 用完没 free，函数返回后 p 丢了，这块内存永远找不回来
}
```

每次 malloc 都要有对应的 free。长运行程序泄漏会越来越多，最后 OOM。

### 7. 越界访问

```c
int a[5] = {0};
a[5] = 100;      // 下标 0~4，a[5] 越界
a[-1] = 1;       // 也是越界
```

编译器不报错，但踩的是相邻变量或堆元数据。

### 8. 栈上返回地址

```c
int *bad(void) {
    int x = 42;
    return &x;    // 错误！x 在函数返回后就销毁了
}
```

返回局部变量地址是经典错误，函数返回后栈帧回收，那个地址已经不属于你了。

### 怎么发现这些错误

- **开警告**：`gcc -Wall -Wextra`，编译器会帮你抓很多
- **调试器**：gdb 逐步跑，看崩溃在哪一行
- **Valgrind**（Linux/WSL）：专门查内存泄漏和越界
- **AddressSanitizer**：`gcc -fsanitize=address`，编译时插桩，运行时报告错误

```powershell
gcc -Wall -Wextra -fsanitize=address test.c -o test
.\test.exe
```

## 核心重点
- 野指针、空指针解引用、重复 free、use-after-free、溢出、泄漏、越界、返回局部地址——八大金刚
- 指针声明就初始化，free 后置 NULL
- 字符串操作限制长度，用 strncpy/fgets
- 编译加 `-Wall -Wextra -fsanitize=address`
- 永远不要返回局部变量的地址

## 练习/任务
1. 故意写一个野指针程序，用 AddressSanitizer 跑，看报告
2. 故意写一个内存泄漏程序，用 AddressSanitizer 跑，看报告
3. 检查你之前写的所有代码，是否都 free 了 malloc 的内存
4. （选做）写一个"安全"的字符串读入函数，避免溢出

## 打卡清单
- [ ] 认识 8 种常见内存错误
- [ ] 会用 -Wall -Wextra 开警告
- [ ] 会用 AddressSanitizer 定位问题
- [ ] 养成 free 后置 NULL 的习惯
