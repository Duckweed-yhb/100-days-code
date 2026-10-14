# Day 87 · 高频算法题实战一

## 今日目标
- 刷高频面试题
- 串联前几天学的技巧
- 形成题感

## 学习内容

### 题目 1：合并两个有序链表

递归或迭代。比较两个头，小的接结果。

```c
ListNode* mergeTwoLists(ListNode *a, ListNode *b) {
    if (!a) return b;
    if (!b) return a;
    if (a->val < b->val) {
        a->next = mergeTwoLists(a->next, b);
        return a;
    } else {
        b->next = mergeTwoLists(a, b->next);
        return b;
    }
}
```

### 题目 2：环形链表

快慢指针，快的每次两步，慢的一步。相遇即有环。

### 题目 3：二叉树最大深度

```c
int maxDepth(TreeNode *root) {
    if (!root) return 0;
    return 1 + max(maxDepth(root->left), maxDepth(root->right));
}
```

### 题目 4：反转链表

三指针 pre/cur/next。

### 题目 5：爬楼梯 DP

`dp[i] = dp[i-1] + dp[i-2]`。

### 刷题节奏

- 每天 2~3 题
- 先想 15 分钟，想不出来看题解
- 看懂后自己写一遍
- 第二天重做一遍昨天的

## 核心重点
- 高频题反复练
- 技巧：递归、双指针、DP
- 形成条件反射：看到题就知道用什么技巧
- 不在于刷多少，在于每道都吃透

## 练习/任务
1. 合并两个有序链表
2. 环形链表判断
3. 二叉树最大深度
4. 反转链表

## 今日刷题
- **合并两个有序链表**（LeetCode 第 21 题）。
- **环形链表**（LeetCode 第 141 题）。
- **二叉树的最大深度**（LeetCode 第 104 题）。
- **反转链表**（LeetCode 第 206 题）。

## 打卡清单
- [ ] 完成 4 道高频题
- [ ] 理解每道题技巧
- [ ] 不看题解能写
- [ ] 形成题感
