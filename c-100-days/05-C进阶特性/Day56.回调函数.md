# Day 56 · 回调函数

## 今日目标
- 用回调实现通用遍历
- 理解上下文指针（user_data）
- 写一个"通用链表"

## 学习内容

### 通用遍历

Day27 写的链表只能存 int。如果想让链表能存任意类型，怎么办？答案是 void* + 回调。

```c
typedef struct ListNode {
    void *data;
    struct ListNode *next;
} ListNode;

typedef void (*FreeFunc)(void *);
typedef void (*PrintFunc)(void *);

void list_free(ListNode *head, FreeFunc free_data) {
    while (head) {
        ListNode *next = head->next;
        free_data(head->data);   // 调用者决定怎么释放数据
        free(head);
        head = next;
    }
}

void list_print(ListNode *head, PrintFunc print_data) {
    for (ListNode *p = head; p; p = p->next) {
        print_data(p->data);
    }
}
```

链表本身不知道 data 是什么，释放和打印都交给回调。

### 上下文指针

回调函数签名可以带一个 `void *user_data`，让调用者传额外参数：

```c
typedef void (*Visitor)(void *data, void *user_data);

void list_foreach(ListNode *head, Visitor visit, void *user_data) {
    for (ListNode *p = head; p; p = p->next) {
        visit(p->data, user_data);
    }
}
```

### 实际例子：Linux 内核

Linux 内核大量用回调：文件系统操作函数指针表、设备驱动操作函数表。C 没有类和虚函数，就靠函数指针表实现"多态"。

### 回调 vs 函数指针数组

- 回调：把函数传给别人，别人回头调你
- 函数指针数组：根据索引/名字分发到不同函数

两者本质都是函数指针，只是使用模式不同。

## 核心重点
- void* + 回调 = 通用容器
- 链表不知道数据类型，释放打印交给回调
- user_data 让回调带额外参数
- C 用函数指针表模拟面向对象多态
- 这是 C 工程化的核心套路

## 练习/任务
1. 写一个通用链表，data 是 void*
2. 为 int 数据写 print 和 free 回调
3. 为字符串数据写另一套回调
4. （选做）实现 list_foreach 带 user_data

## 今日刷题
- 今天工程为主，复习 qsort 用法即可。

## 打卡清单
- [ ] 理解 void* 通用容器
- [ ] 会写回调函数
- [ ] 理解 user_data 作用
- [ ] 知道 C 怎么模拟多态
