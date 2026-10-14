# Day 27 · 单链表实现

## 今日目标
- 理解链表节点结构
- 实现单链表的增删查打印
- 体会链式存储与顺序存储的差异

## 学习内容

### 节点定义

```c
typedef struct Node {
    int data;
    struct Node *next;
} Node;
```

每个节点存一个数据和一个指向下一节点的指针。最后一个节点的 `next` 是 `NULL`。

### 创建节点

```c
Node *new_node(int value) {
    Node *n = malloc(sizeof(Node));
    n->data = value;
    n->next = NULL;
    return n;
}
```

### 头插法建表

```c
Node *head = NULL;

// 在头部插入
void push_front(Node **head, int value) {
    Node *n = new_node(value);
    n->next = *head;
    *head = n;
}
```

注意 head 本身要改，所以参数是 `Node **head`。

### 遍历

```c
void print_list(Node *head) {
    Node *p = head;
    while (p != NULL) {
        printf("%d -> ", p->data);
        p = p->next;
    }
    printf("NULL\n");
}
```

### 尾插法

```c
void push_back(Node **head, int value) {
    Node *n = new_node(value);
    if (*head == NULL) {
        *head = n;
        return;
    }
    Node *p = *head;
    while (p->next != NULL) p = p->next;
    p->next = n;
}
```

### 按值删除

```c
void remove_value(Node **head, int target) {
    Node **pp = head;
    while (*pp != NULL) {
        if ((*pp)->data == target) {
            Node *del = *pp;
            *pp = (*pp)->next;   // 绕过被删节点
            free(del);
        } else {
            pp = &(*pp)->next;
        }
    }
}
```

### 释放整个链表

```c
void free_list(Node *head) {
    while (head != NULL) {
        Node *next = head->next;
        free(head);
        head = next;
    }
}
```

**必须先存 next 再 free**，否则 free 后就读不到 head->next 了。

### 使用示例

```c
int main(void) {
    Node *head = NULL;
    push_back(&head, 10);
    push_back(&head, 20);
    push_front(&head, 5);
    print_list(head);      // 5 -> 10 -> 20 -> NULL
    free_list(head);
    return 0;
}
```

## 核心重点
- 节点 = data + next 指针
- 头插 O(1)，尾插要遍历到尾 O(n)
- 删除节点要先保存 next 再 free
- 修改头指针时函数参数要用二级指针
- 链表释放要逐个 free，不能只 free 头

## 练习/任务
1. 实现单链表的 push_front、push_back、print_list、free_list
2. 实现 `int find(Node *head, int target)` 查找值是否存在
3. 实现 `int length(Node *head)` 求链表长度
4. （选做）实现 `void reverse(Node **head)` 反转链表

## 今日刷题
- **反转链表**（LeetCode 第 206 题）：三指针 pre/cur/next 逐个反转指向。
- **链表的中间节点**（LeetCode 第 876 题）：快慢指针，快的每次走两步，慢的走一步，快的到尾时慢的在中间。
- **合并两个有序链表**（LeetCode 第 21 题）：比较两链表头，小的接结果，递归或迭代均可。

## 打卡清单
- [ ] 理解链表节点结构
- [ ] 实现链表增删打印释放
- [ ] 理解为什么删除要用二级指针或前驱指针
- [ ] 完成反转链表练习
