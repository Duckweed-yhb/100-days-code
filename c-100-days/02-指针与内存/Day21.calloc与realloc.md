# Day 21 · calloc与realloc

## 今日目标
- 掌握 `calloc` 与 `malloc` 的区别
- 学会用 `realloc` 调整已分配内存大小
- 理解动态扩容数组的原理

## 学习内容

### calloc：分配并清零

```c
#include <stdlib.h>

int *arr = calloc(10, sizeof(int));
// 等价于：
// int *arr = malloc(10 * sizeof(int));
// memset(arr, 0, 10 * sizeof(int));
```

区别：
- `malloc(n * sizeof(int))` 不初始化，内存里是垃圾值
- `calloc(个数, 每个大小)` 会把内存全部清零

申请大量内存且需要初始为 0 时，calloc 一步到位。

### realloc：调整大小

```c
int *arr = malloc(5 * sizeof(int));
// 填入 5 个元素...

// 现在不够了，要扩容到 10 个
arr = realloc(arr, 10 * sizeof(int));
```

`realloc(旧指针, 新大小)`：
- 尽量在原地址扩展，内容保留
- 原位置后面没有足够空间时，会**新找一块足够大的内存**，把旧数据拷过去，释放旧块，返回新地址

### realloc 的坑

**不能直接写 `arr = realloc(arr, new_size)`**：如果 realloc 失败返回 NULL，你原来的 arr 就丢了，内存泄漏。正确写法：

```c
int *tmp = realloc(arr, new_size * sizeof(int));
if (tmp == NULL) {
    // 分配失败，arr 仍然有效，处理错误
    free(arr);
    return;
}
arr = tmp;   // 成功了再覆盖
```

### 动态扩容数组原理

这就是 Python list、C++ vector 背后的思想：

```c
typedef struct {
    int *data;
    int  size;    // 已有元素数
    int  cap;     // 容量
} Vec;

void vec_push(Vec *v, int x) {
    if (v->size == v->cap) {
        int new_cap = v->cap == 0 ? 4 : v->cap * 2;
        int *tmp = realloc(v->data, new_cap * sizeof(int));
        if (tmp == NULL) return;
        v->data = tmp;
        v->cap = new_cap;
    }
    v->data[v->size++] = x;
}
```

每次满了就翻倍扩容，均摊 O(1) 复杂度。

### memset：批量填值

```c
#include <string.h>

memset(ptr, 0, size);      // 全部清零
memset(ptr, 0xFF, size);   // 每字节填 0xFF
```

注意 `memset` 是按字节填的，不要用它填非 0/非 0xFF 的 int（如 `memset(arr, 1, n*sizeof(int))` 得到的不是 1）。

### 三个函数对比

| 函数 | 用途 | 初始化 |
|---|---|---|
| `malloc(n)` | 分配 n 字节 | 不初始化 |
| `calloc(c, s)` | 分配 c×s 字节 | 清零 |
| `realloc(p, n)` | 把 p 调整为 n 字节 | 保留旧内容，新区域不初始化 |

## 核心重点
- calloc = malloc + 清零
- realloc 扩容可能移动整块内存，旧指针失效
- realloc 要用临时指针接收，失败不能丢原指针
- 动态数组靠"满了就翻倍"实现均摊 O(1)
- memset 按字节填充，只适合填 0 或 0xFF

## 练习/任务
1. 用 calloc 分配 100 个 int，打印它们的值（应该全是 0）
2. 写一个动态数组：初始分配 4 个，不断 push，满了 realloc 翻倍
3. 用 realloc 把 5 个元素的数组扩容到 10，打印前 5 个元素是否保留
4. （选做）实现一个完整的 Vec 结构：push、pop、get、free

## 打卡清单
- [ ] 区分 malloc 与 calloc
- [ ] 正确使用 realloc（用临时指针）
- [ ] 理解动态扩容均摊 O(1) 原理
- [ ] 会用 memset 清零内存
