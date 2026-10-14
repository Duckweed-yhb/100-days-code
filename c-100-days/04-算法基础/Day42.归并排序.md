# Day 42 · 归并排序

## 今日目标
- 理解归并排序"先分后合"
- 手写归并排序
- 对比快排与归并

## 学习内容

### 归并排序思路

分：把数组从中间切成两半，递归排序左右。
合：把两个已经有序的子数组合并成一个大的有序数组。

### 实现

```c
int tmp[100000];   // 辅助数组

void merge(int *a, int left, int mid, int right) {
    int i = left, j = mid + 1, k = left;
    while (i <= mid && j <= right) {
        if (a[i] <= a[j]) tmp[k++] = a[i++];
        else              tmp[k++] = a[j++];
    }
    while (i <= mid)  tmp[k++] = a[i++];
    while (j <= right) tmp[k++] = a[j++];
    for (int t = left; t <= right; t++) a[t] = tmp[t];
}

void merge_sort(int *a, int left, int right) {
    if (left >= right) return;
    int mid = (left + right) / 2;
    merge_sort(a, left, mid);
    merge_sort(a, mid + 1, right);
    merge(a, left, mid, right);
}
```

### 复杂度

- 时间：稳定 O(n log n)，无论最好最坏
- 空间：O(n)，需要辅助数组

### 快排 vs 归并

| 方面 | 快排 | 归并 |
|---|---|---|
| 时间平均 | O(n log n) | O(n log n) |
| 时间最坏 | O(n²) | O(n log n) |
| 空间 | O(log n) 栈 | O(n) 辅助数组 |
| 稳定性 | 不稳定 | 稳定 |
| 适合 | 内存排序 | 链表、外部排序 |

归并排序稳定且最坏也 O(n log n)，代价是额外空间。

### 应用：逆序对

归并排序合并时，如果右边元素先于左边取走，就说明它比左边剩余所有元素都小，构成逆序对。这是经典的逆序对计数算法。

## 核心重点
- 归并 = 递归分半 + 合并两个有序数组
- 稳定 O(n log n)，需要 O(n) 辅助空间
- 稳定排序
- 适合链表排序和外部排序
- 可用来数逆序对

## 练习/任务
1. 手写归并排序
2. 对比快排和归并在已有序数组上的表现
3. 用归并思想合并两个有序数组
4. （选做）用归并排序统计逆序对个数

## 今日刷题
- **排序数组**（LeetCode 第 912 题）：用归并排序再写一遍。
- **合并两个有序数组**（LeetCode 第 88 题）：归并的核心操作。
- **排序链表**（LeetCode 第 148 题）：归并排序在链表上的应用。

## 打卡清单
- [ ] 理解先分后合
- [ ] 手写归并排序
- [ ] 对比快排与归并
- [ ] 知道归并稳定且最坏 O(n log n)
