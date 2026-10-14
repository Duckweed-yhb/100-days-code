# Day 25 · 指针阶段综合项目

## 今日目标
- 综合运用指针与动态内存完成一个小项目
- 实现一个"动态字符串列表"
- 验收前两周学习成果

## 学习内容

### 项目：动态字符串数组

实现一个可以随时添加字符串的动态数组，支持：
- 初始化
- 添加字符串（自动扩容）
- 按下标访问
- 打印所有内容
- 销毁（释放所有内存）

### 完整代码

```c
#include <stdio.h>
#include <stdlib.h>
#include <string.h>

typedef struct {
    char **data;   // 字符串指针数组
    int    size;   // 已有多少个
    int    cap;    // 容量
} StrList;

// 初始化
void list_init(StrList *list) {
    list->data = NULL;
    list->size = 0;
    list->cap  = 0;
}

// 添加字符串
void list_push(StrList *list, const char *s) {
    if (list->size == list->cap) {
        int new_cap = list->cap == 0 ? 4 : list->cap * 2;
        char **tmp = realloc(list->data, new_cap * sizeof(char*));
        if (!tmp) return;
        list->data = tmp;
        list->cap  = new_cap;
    }
    // 为字符串本身分配内存并拷贝
    list->data[list->size] = malloc(strlen(s) + 1);
    strcpy(list->data[list->size], s);
    list->size++;
}

// 打印
void list_print(const StrList *list) {
    for (int i = 0; i < list->size; i++) {
        printf("[%d] %s\n", i, list->data[i]);
    }
}

// 销毁
void list_free(StrList *list) {
    for (int i = 0; i < list->size; i++) {
        free(list->data[i]);      // 先释放每个字符串
    }
    free(list->data);             // 再释放指针数组
    list->data = NULL;
    list->size = list->cap = 0;
}

int main(void) {
    StrList list;
    list_init(&list);

    list_push(&list, "Apple");
    list_push(&list, "Banana");
    list_push(&list, "Cherry");
    list_push(&list, "Date");
    list_push(&list, "Elderberry");   // 触发扩容

    list_print(&list);
    list_free(&list);
    return 0;
}
```

### 代码要点解读

1. **两层指针**：`char **data` 是指针数组的首地址，每个元素是 `char *`
2. **两层分配**：外层 `realloc` 扩容指针数组，内层 `malloc` 存字符串内容
3. **两层释放**：先 `free(data[i])` 每个字符串，再 `free(data)` 指针数组
4. **自动扩容**：满了翻倍，和 Day21 讲的动态数组原理一致

### 编译运行

```powershell
gcc list.c -o list.exe
.\list.exe
```

预期输出：

```
[0] Apple
[1] Banana
[2] Cherry
[3] Date
[4] Elderberry
```

## 核心重点
- 动态字符串列表是"指针数组 + 动态扩容"的综合应用
- 两层结构：外层指针数组管理容量，内层每个字符串独立堆分配
- 释放顺序与分配顺序相反
- 用结构体封装数据和操作，是 C 模块化的基本套路

## 练习/任务
1. 把上面的代码敲一遍，编译运行成功
2. 给 StrList 增加一个 `list_remove(list, idx)` 函数，删除第 idx 个字符串（记得释放内存）
3. 增加 `list_find(list, name)` 函数，查找字符串是否存在，返回下标
4. （选做）让程序从文件读入字符串列表（文件 I/O Day60 才学，可先跳过）

## 今日刷题
- **反转字符串**（LeetCode 第 344 题）：输入字符数组，原地反转。思路：左右双指针向中间交换，O(n) 时间 O(1) 空间。
- **移除元素**（LeetCode 第 27 题）：原地删除数组中所有等于 val 的元素。思路：快慢指针，慢指针记录写入位置，快指针扫描。
- **替换空格**（剑指 Offer）：把字符串中的空格替换成 `%20`。思路：先统计空格数计算新长度，再从后往前拷贝避免覆盖。

## 打卡清单
- [ ] 理解两层指针的内存结构
- [ ] 成功编译运行 StrList 程序
- [ ] 实现 list_remove 和 list_find
- [ ] 阶段项目完成，指针部分达标
