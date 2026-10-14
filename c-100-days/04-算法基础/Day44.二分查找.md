# Day 44 · 二分查找

## 今日目标
- 理解二分查找 O(log n) 的威力
- 手写标准二分查找
- 知道二分的前提是有序

## 学习内容

### 为什么需要二分

在 1 亿个有序数里找一个数，逐个找最坏 1 亿次，二分只要 27 次（log₂10⁸ ≈ 27）。

### 标准二分

```c
int binary_search(int *a, int n, int target) {
    int left = 0, right = n - 1;
    while (left <= right) {
        int mid = left + (right - left) / 2;   // 防溢出
        if (a[mid] == target) return mid;
        else if (a[mid] < target) left = mid + 1;
        else                       right = mid - 1;
    }
    return -1;
}
```

关键点：
- `mid = left + (right-left)/2` 而不是 `(left+right)/2`，防止大数相加溢出
- 循环条件 `left <= right`
- 找到返回下标，找不到返回 -1

### 为什么是 O(log n)

每次砍掉一半：n → n/2 → n/4 → ... → 1，共 log₂n 次。

### 二分的前提

**数组必须有序**。无序数组不能二分，得先排序（排序 O(n log n) 一次性，后续每次查找 O(log n)）。

### 整数溢出问题

`(left + right) / 2` 当 left 和 right 都接近 INT_MAX 时相加溢出。`left + (right - left)/2` 永远安全。

## 核心重点
- 二分查找每次砍一半，O(log n)
- 前提：数组有序
- mid 计算防溢出：left + (right-left)/2
- 循环条件 left <= right
- 找到返回下标，找不到返回 -1

## 练习/任务
1. 在有序数组中二分查找 target，存在返回下标
2. 比较二分与顺序查找在 n=10^6 时的速度差
3. 思考：为什么不能对无序数组二分？
4. （选做）实现二分查找的递归版本

## 今日刷题
- **二分查找**（LeetCode 第 704 题）：标准模板题。
- **搜索插入位置**（LeetCode 第 35 题）：找不到时返回应该插入的位置。
- **猜数字大小**（LeetCode 第 374 题）：交互题，本质二分。

## 打卡清单
- [ ] 手写二分查找
- [ ] 理解 O(log n) 的来源
- [ ] 知道 mid 防溢出写法
- [ ] 明白二分前提是有序
