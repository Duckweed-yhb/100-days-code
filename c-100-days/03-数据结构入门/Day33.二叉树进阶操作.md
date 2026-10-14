# Day 33 · 二叉树进阶操作

## 今日目标
- 实现二叉搜索树的插入、查找
- 学会镜像、判断平衡等常见操作
- 理解 BST 性质

## 学习内容

### 二叉搜索树 BST

左子树所有节点值 < 根 < 右子树所有节点值。这个性质让查找像二分查找一样 O(log n)。

### 查找

```c
TreeNode *bst_search(TreeNode *root, int key) {
    if (root == NULL || root->val == key) return root;
    if (key < root->val) return bst_search(root->left, key);
    else                 return bst_search(root->right, key);
}
```

### 插入

```c
TreeNode *bst_insert(TreeNode *root, int key) {
    if (root == NULL) return make_node(key);
    if (key < root->val)
        root->left = bst_insert(root->left, key);
    else if (key > root->val)
        root->right = bst_insert(root->right, key);
    return root;
}
```

### 求高度

```c
int height(TreeNode *root) {
    if (root == NULL) return 0;
    int lh = height(root->left);
    int rh = height(root->right);
    return (lh > rh ? lh : rh) + 1;
}
```

### 镜像翻转

```c
void mirror(TreeNode *root) {
    if (root == NULL) return;
    TreeNode *t = root->left;
    root->left = root->right;
    root->right = t;
    mirror(root->left);
    mirror(root->right);
}
```

### 判断两棵树是否相同

```c
int is_same(TreeNode *a, TreeNode *b) {
    if (a == NULL && b == NULL) return 1;
    if (a == NULL || b == NULL) return 0;
    return a->val == b->val &&
           is_same(a->left, b->left) &&
           is_same(a->right, b->right);
}
```

### 删除整棵树

后序遍历：先删左右，再 free 当前节点：

```c
void tree_free(TreeNode *root) {
    if (root == NULL) return;
    tree_free(root->left);
    tree_free(root->right);
    free(root);
}
```

### BST 的退化问题

如果按顺序插入 1、2、3、4、5，BST 会退化成链表，查找 O(n)。这就是后面学 AVL、红黑树的动机。

## 核心重点
- BST 左 < 根 < 右，查找插入平均 O(log n)
- 树的操作大多是递归：处理当前 + 递归左右
- 后序遍历适合删除整棵树
- BST 最坏退化成链表，需要平衡树（后面学）
- 镜像、相同判断、高度都是经典递归题

## 练习/任务
1. 实现 BST 插入和查找
2. 写函数判断两棵树是否相同
3. 写函数求二叉树叶子节点个数
4. （选做）实现 BST 删除节点（三种情况：叶子、单子、双子）

## 今日刷题
- **相同的树**（LeetCode 第 100 题）：双树同步递归比较。
- **对称二叉树**（LeetCode 第 101 题）：左树与右树镜像递归比较。
- **路径总和**（LeetCode 第 112 题）：从根到叶是否有一条路径和等于 target。

## 打卡清单
- [ ] 理解 BST 性质
- [ ] 实现 BST 插入查找
- [ ] 会写树的递归操作
- [ ] 理解为什么 BST 可能退化
