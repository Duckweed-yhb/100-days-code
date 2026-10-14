# Day 28 · 双向链表

## 今日目标
- 理解双向链表的结构优势
- 实现双向链表的增删操作
- 对比单链表与双向链表

## 学习内容

### 为什么需要双向链表

单链表只能向前走，删除一个节点必须找到它的前驱。双向链表每个节点多一个 `prev` 指针，可以双向遍历，删除已知节点本身即可 O(1) 完成。

### 节点定义

```c
typedef struct DNode {
    int data;
    struct DNode *prev;
    struct DNode *next;
} DNode;
```

### 尾部插入

```c
void push_back(DNode **head, int value) {
    DNode *n = malloc(sizeof(DNode));
    n->data = value;
    n->next = NULL;

    if (*head == NULL) {
        n->prev = NULL;
        *head = n;
        return;
    }
    DNode *p = *head;
    while (p->next) p = p->next;
    p->next = n;
    n->prev = p;
}
```

### 删除已知节点

```c
void remove_node(DNode **head, DNode *del) {
    if (del->prev) del->prev->next = del->next;
    else           *head = del->next;     // 删的是头
    if (del->next) del->next->prev = del->prev;
    free(del);
}
```

因为有 `prev`，不需要遍历找前驱。

### 双向链表 vs 单链表

| 方面 | 单链表 | 双向链表 |
|---|---|---|
| 指针数 | 1 个 next | prev + next |
| 反向遍历 | 不行 | 可以 |
| 删除已知节点 | O(n) | O(1) |
| 空间开销 | 小 | 大一倍指针 |

### 循环链表

让尾节点的 next 指回头节点（而不是 NULL），就是循环链表。可以用来实现轮转任务队列。

### 实际应用

- Linux 内核里大量使用双向链表（`struct list_head`）
- 浏览器前进后退（双链表）
- LRU 缓存淘汰（双向链表 + 哈希表）

## 核心重点
- 双向链表多一个 prev 指针，支持双向遍历
- 删除已知节点 O(1)，不用遍历找前驱
- 插入删除要同时维护 prev 和 next 两组指针
- 空间换时间：双向链表比单链表灵活但占更多内存

## 练习/任务
1. 实现双向链表的 push_front、push_back、print（正序）、print_reverse（逆序）
2. 实现按值删除双向链表节点
3. 写一个函数判断链表是否有环（快慢指针）
4. （选做）把单链表改造成循环链表

## 今日刷题
- **环形链表 II**（LeetCode 第 142 题）：找入环节点。思路：快慢指针相遇后，一个回到头，两个同速走，再次相遇即入口。
- **相交链表**（LeetCode 第 160 题）：找两链表交点。思路：双指针走完自己的接对方的，路程相同时在交点相遇。
- **删除链表倒数第 N 个节点**（LeetCode 第 19 题）：快慢指针，快的先走 N 步。

## 打卡清单
- [ ] 理解双向链表结构
- [ ] 能实现双向链表插入删除
- [ ] 会用快慢指针判断环
- [ ] 对比单双向链表差异
