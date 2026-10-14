# Day 29 · 栈的实现与应用

## 今日目标
- 理解栈"后进先出"（LIFO）特性
- 用数组实现栈
- 解决括号匹配等经典问题

## 学习内容

### 栈是什么

栈是一种只能在一端（栈顶）操作的线性表。最后放入的最先取出，叫**后进先出 LIFO**。

### 数组实现

```c
#define MAX 100
typedef struct {
    int data[MAX];
    int top;     // 栈顶下标，-1 表示空
} Stack;

void init(Stack *s) { s->top = -1; }

int is_empty(Stack *s) { return s->top == -1; }
int is_full(Stack *s)  { return s->top == MAX - 1; }

void push(Stack *s, int v) {
    if (is_full(s)) return;
    s->data[++s->top] = v;
}

int pop(Stack *s) {
    if (is_empty(s)) return -1;
    return s->data[s->top--];
}

int peek(Stack *s) {
    return s->data[s->top];
}
```

### 应用 1：函数调用栈

C 程序调用函数时，返回地址、参数、局部变量压栈；函数返回时弹栈。递归太深栈溢出就是这个原因。

### 应用 2：括号匹配

```c
int match(const char *s) {
    Stack st;
    init(&st);
    for (const char *p = s; *p; p++) {
        if (*p == '(' || *p == '[' || *p == '{') {
            push(&st, *p);
        } else if (*p == ')' || *p == ']' || *p == '}') {
            if (is_empty(&st)) return 0;
            int t = pop(&st);
            if ((*p == ')' && t != '(') ||
                (*p == ']' && t != '[') ||
                (*p == '}' && t != '{')) return 0;
        }
    }
    return is_empty(&st);
}
```

左括号压栈，右括号弹栈匹配，最后栈空就是合法。

### 应用 3：表达式求值（后缀表达式）

栈是计算器的核心：遇到数字压栈，遇到运算符弹出两个数计算，结果压栈。

### 动态栈

数组定长栈空间固定，可用 Day21 学的动态数组扩容实现不限长栈。

## 核心重点
- 栈 LIFO：push 入栈、pop 出栈、peek 看栈顶
- 数组实现：top 下标 -1 表示空
- 括号匹配是栈的经典应用
- 函数调用靠栈实现
- 栈是深度优先搜索（DFS）的底层结构

## 练习/任务
1. 用数组实现 int 栈，push/pop/peek/isEmpty
2. 写一个函数判断字符串中括号是否匹配
3. 用两个栈实现一个队列（思路：入队压栈 A，出队时把 A 倒进 B）
4. （选做）计算后缀表达式（逆波兰表达式）

## 今日刷题
- **有效的括号**（LeetCode 第 20 题）：用栈匹配 () [] {}，左括号压栈右括号弹栈。
- **用栈实现队列**（LeetCode 第 232 题）：两个栈，一个入一个出。
- **最小栈**（LeetCode 第 155 题）：额外维护一个栈记录当前最小值。

## 打卡清单
- [ ] 理解 LIFO
- [ ] 用数组实现栈
- [ ] 解决括号匹配问题
- [ ] 理解栈与函数调用的关系
