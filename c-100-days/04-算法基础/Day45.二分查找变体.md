# Day 45 · 二分查找变体

## 今日目标
- 掌握"找左边界"和"找右边界"
- 处理重复元素的二分
- 理解"二分答案"思想

## 学习内容

### 问题：有重复元素怎么办

标准二分找到一个 target 就停了，但如果数组有多个 target，找最左/最右的那个？

### 找左边界（第一个 >= target 的位置）

```c
int lower_bound(int *a, int n, int target) {
    int left = 0, right = n;
    while (left < right) {
        int mid = left + (right - left) / 2;
        if (a[mid] < target) left = mid + 1;
        else                 right = mid;
    }
    return left;
}
```

返回的是第一个大于等于 target 的下标。如果 `a[left] == target`，就是 target 最左位置。

### 找右边界（第一个 > target 的位置）

```c
int upper_bound(int *a, int n, int target) {
    int left = 0, right = n;
    while (left < right) {
        int mid = left + (right - left) / 2;
        if (a[mid] <= target) left = mid + 1;
        else                  right = mid;
    }
    return left;
}
```

### 统计 target 出现次数

```c
int count = upper_bound(a, n, target) - lower_bound(a, n, target);
```

两行搞定。

### 二分答案思想

不是"在数组里找数"，而是"猜答案，验证"。比如：

> 把一根长木头切成 k 段等长小段，求最长小段长度。

答案范围是 [0, max]，二分猜一个长度，验证能不能切出 k 段。这就是"二分答案"。

### 二分的模板

左闭右开 `[left, right)` 写法更统一，所有变体都能套：

```c
while (left < right) {
    int mid = left + (right - left) / 2;
    if (条件满足) right = mid;   // 收缩右边界
    else          left = mid + 1;
}
return left;
```

## 核心重点
- lower_bound：第一个 >= target
- upper_bound：第一个 > target
- 两者相减 = target 出现次数
- 二分答案：猜答案 + 验证
- 左闭右开模板统一好记

## 练习/任务
1. 实现 lower_bound 和 upper_bound
2. 用它们统计 target 在有序数组中出现次数
3. 实现"木头切 k 段"二分答案题
4. （选做）在旋转有序数组中找 target（LeetCode 33）

## 今日刷题
- **在排序数组中查找元素的第一个和最后一个位置**（LeetCode 第 34 题）：lower_bound + upper_bound。
- **搜索旋转排序数组**（LeetCode 第 33 题）：先找断点再二分。
- **x 的平方根**（LeetCode 第 69 题）：二分答案。

## 打卡清单
- [ ] 会写 lower_bound / upper_bound
- [ ] 理解二分答案思想
- [ ] 处理重复元素
- [ ] 掌握左闭右开模板
