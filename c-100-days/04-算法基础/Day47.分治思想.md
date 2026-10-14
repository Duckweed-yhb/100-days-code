# Day 47 · 分治思想

## 今日目标
- 理解分治三步走：分解、解决、合并
- 认识分治的经典案例
- 知道分治与递归的关系

## 学习内容

### 分治三步

1. **分解**：把问题分成若干规模更小的子问题
2. **解决**：递归求解子问题
3. **合并**：把子问题的答案组合成原问题答案

### 已经见过的分治

- **归并排序**：分成两半排序，再合并
- **快速排序**：分区后递归排序两边
- **二分查找**：砍一半找一边
- **二叉树遍历**：左子树 + 根 + 右子树

### 经典案例：最大子数组和

数组中有正有负，求连续子数组最大和。分治思路：
- 数组从中点切
- 最大子数组要么在左半、要么在右半、要么跨中点
- 左半右半递归求
- 跨中点的情况从中点往两边扫

```c
int max_crossing(int *a, int l, int m, int r) {
    int left_sum = -1e9, sum = 0;
    for (int i = m; i >= l; i--) {
        sum += a[i];
        if (sum > left_sum) left_sum = sum;
    }
    int right_sum = -1e9; sum = 0;
    for (int i = m+1; i <= r; i++) {
        sum += a[i];
        if (sum > right_sum) right_sum = sum;
    }
    return left_sum + right_sum;
}

int max_subarray(int *a, int l, int r) {
    if (l == r) return a[l];
    int m = (l + r) / 2;
    int L = max_subarray(a, l, m);
    int R = max_subarray(a, m+1, r);
    int C = max_crossing(a, l, m, r);
    int best = L;
    if (R > best) best = R;
    if (C > best) best = C;
    return best;
}
```

O(n log n)。（后面 DP 有 O(n) 解法。）

### 分治 vs 动态规划

分治的子问题互相独立；DP 的子问题会重叠（后面学）。

## 核心重点
- 分治 = 分解 + 解决 + 合并
- 归并、快排、二分都是分治
- 子问题互相独立是分治的标志
- 分治天然用递归实现
- 最大子数组和是经典分治题

## 练习/任务
1. 用分治实现 Pow(x, n)（O(log n)）
2. 用分治求数组最大值
3. 用分治求逆序对个数
4. （选做）实现最大子数组和分治版

## 今日刷题
- **多数元素**（LeetCode 第 169 题）：分治或摩尔投票。
- **合并两个有序链表**（LeetCode 第 21 题）：分治合并。
- **不同的二叉搜索树 II**（LeetCode 第 95 题）：枚举根 + 递归左右。

## 打卡清单
- [ ] 理解分治三步
- [ ] 知道分治与递归关系
- [ ] 会写简单分治算法
- [ ] 区分分治与动态规划
