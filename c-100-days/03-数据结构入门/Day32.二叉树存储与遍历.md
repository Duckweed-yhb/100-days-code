# Day 32 · 二叉树存储与遍历

## 今日目标
- 掌握二叉链表表示法
- 实现前序、中序、后序、层序四种遍历
- 理解递归在树操作中的核心地位

## 学习内容

### 二叉树节点

```c
typedef struct TreeNode {
    int val;
    struct TreeNode *left;
    struct TreeNode *right;
} TreeNode;
```

### 前序遍历（根左右）

```c
void preorder(TreeNode *root) {
    if (root == NULL) return;
    printf("%d ", root->val);       // 访问根
    preorder(root->left);           // 左子树
    preorder(root->right);          // 右子树
}
```

### 中序遍历（左根右）

```c
void inorder(TreeNode *root) {
    if (root == NULL) return;
    inorder(root->left);
    printf("%d ", root->val);
    inorder(root->right);
}
```

BST 的中序遍历结果是**升序排列**，这是 BST 的重要性质。

### 后序遍历（左右根）

```c
void postorder(TreeNode *root) {
    if (root == NULL) return;
    postorder(root->left);
    postorder(root->right);
    printf("%d ", root->val);
}
```

后序遍历适合"先处理子节点再处理自己"的场景，比如删除树（先删左右再 free 当前节点）。

### 三种遍历记忆法

- 前序：第一次经过节点就访问
- 中序：从左子树回来时访问
- 后序：从右子树回来时访问

### 层序遍历（BFS）

按层从上到下、从左到右访问。需要用队列：

```c
void level_order(TreeNode *root) {
    if (root == NULL) return;
    // 用数组模拟队列
    TreeNode *q[100];
    int head = 0, tail = 0;
    q[tail++] = root;
    while (head < tail) {
        TreeNode *cur = q[head++];
        printf("%d ", cur->val);
        if (cur->left)  q[tail++] = cur->left;
        if (cur->right) q[tail++] = cur->right;
    }
}
```

层序遍历就是 Day30 队列的经典应用。

### 创建一棵二叉树

```c
TreeNode *make_node(int v) {
    TreeNode *n = malloc(sizeof(TreeNode));
    n->val = v;
    n->left = n->right = NULL;
    return n;
}

// 手搓一棵：
//       1
//      / \
//     2   3
//    / \
//   4   5
TreeNode *root = make_node(1);
root->left = make_node(2);
root->right = make_node(3);
root->left->left = make_node(4);
root->left->right = make_node(5);
```

## 核心重点
- 二叉树节点 = val + left + right
- 前序根左右、中序左根右、后序左右根
- BST 中序遍历得到升序
- 层序遍历靠队列实现 BFS
- 树的遍历天然用递归，简洁优美

## 练习/任务
1. 实现二叉树的前中后序遍历
2. 实现层序遍历
3. 写函数求二叉树节点个数
4. （选做）写函数求二叉树高度

## 今日刷题
- **二叉树的前序遍历**（LeetCode 第 144 题）：递归或用栈模拟。
- **二叉树的中序遍历**（LeetCode 第 94 题）：递归一行核心。
- **二叉树的最大深度**（LeetCode 第 104 题）：max(左高, 右高) + 1，递归。

## 打卡清单
- [ ] 理解二叉链表结构
- [ ] 实现三种深度优先遍历
- [ ] 实现层序遍历
- [ ] 理解递归在树操作中的作用
