# Day 30 · 队列与循环队列

## 今日目标
- 理解队列"先进先出"（FIFO）
- 用数组实现普通队列与循环队列
- 了解队列在 BFS 中的作用

## 学习内容

### 队列是什么

队列是只能在队尾插入、队头删除的线性表。先来先服务，叫**先进先出 FIFO**。

### 普通数组队列的问题

朴素实现：head 指向队头，tail 指向队尾。出队 `head++`，入队 `tail++`。问题是 head 一直往后移，前面空出的空间用不上，数组越用越短。

### 循环队列

把数组想象成环形，head 和 tail 绕着走：

```c
#define MAX 100
typedef struct {
    int data[MAX];
    int head;
    int tail;
    int count;      // 当前元素个数，区分空和满
} Queue;

void init(Queue *q) { q->head = q->tail = q->count = 0; }

int is_empty(Queue *q) { return q->count == 0; }
int is_full(Queue *q)  { return q->count == MAX; }

void enqueue(Queue *q, int v) {
    if (is_full(q)) return;
    q->data[q->tail] = v;
    q->tail = (q->tail + 1) % MAX;
    q->count++;
}

int dequeue(Queue *q) {
    if (is_empty(q)) return -1;
    int v = q->data[q->head];
    q->head = (q->head + 1) % MAX;
    q->count--;
    return v;
}
```

关键是 `% MAX` 让下标绕回开头。用 `count` 区分空满，比空一个格子更直观。

### 队列 vs 栈

| 结构 | 顺序 | 典型应用 |
|---|---|---|
| 栈 LIFO | 后进先出 | 括号匹配、函数调用、DFS |
| 队列 FIFO | 先进先出 | 排队、BFS、消息队列 |

### 实际应用

- 打印机任务队列
- 操作系统进程调度
- 广度优先搜索（BFS）：一层层往外扩，必须用队列

### 链表实现队列

链表也能实现队列：head 删、tail 插，都是 O(1)。不需要预分配固定大小。

## 核心重点
- 队列 FIFO：enqueue 队尾入，dequeue 队头出
- 循环队列用 `% size` 绕回
- 用 count 或留空一格区分空满
- BFS 是队列最重要的应用
- 栈 DFS、队列 BFS

## 练习/任务
1. 用数组实现循环队列，enqueue/dequeue/isEmpty/isFull
2. 用链表实现队列
3. 模拟打印任务队列：用户输入任务，按 FIFO 出队
4. （选做）实现双端队列 deque：两端都能进出

## 今日刷题
- **用队列实现栈**（LeetCode 第 225 题）：两个队列，入队时把新元素放到队尾，出队时把前面 N-1 个倒到另一个队列。
- **最近的请求次数**（LeetCode 第 933 题）：队列保存 3000 秒内的请求，来新请求时把队头过期的弹出。
- **滑动窗口最大值**（LeetCode 第 239 题）：用双端队列维护窗口内递减序列。

## 打卡清单
- [ ] 理解 FIFO
- [ ] 实现循环队列
- [ ] 区分栈与队列的应用场景
- [ ] 知道 BFS 要用队列
