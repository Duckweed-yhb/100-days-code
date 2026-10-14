# Day 49 · 回溯经典问题

## 今日目标
- 掌握 N 皇后
- 掌握括号生成
- 理解棋盘类回溯的坐标建模

## 学习内容

### N 皇后

在 n×n 棋盘放 n 个皇后，互相不能攻击（同行、同列、同斜线都不行）。

思路：逐行放，每行选一列。列占用、主对角线（行-列 固定）、副对角线（行+列 固定）用数组记录。

```c
int col[20], diag1[40], diag2[40];
int board[20];

void solve(int row, int n) {
    if (row == n) {
        // 找到一组解
        return;
    }
    for (int c = 0; c < n; c++) {
        if (col[c] || diag1[row-c+n] || diag2[row+c]) continue;
        col[c] = diag1[row-c+n] = diag2[row+c] = 1;
        board[row] = c;
        solve(row + 1, n);
        col[c] = diag1[row-c+n] = diag2[row+c] = 0;
    }
}
```

### 括号生成

生成 n 对括号的所有合法组合。

思路：左括号随时可以放，右括号只能在已放左括号 > 右括号时放。

```c
char buf[30];
void gen(int left, int right, int n) {
    if (left == n && right == n) {
        buf[2*n] = '\0';
        printf("%s\n", buf);
        return;
    }
    if (left < n) {
        buf[left+right] = '(';
        gen(left + 1, right, n);
    }
    if (right < left) {
        buf[left+right] = ')';
        gen(left, right + 1, n);
    }
}
```

### 单词搜索

在二维网格里找单词：从任意格子出发，上下左右走，不能重复走格子。回溯 + visited 数组。

### 回溯题通用套路

1. 画决策树：每一层做什么选择
2. 定结束条件
3. 写选择 → 递归 → 撤销
4. 剪枝：什么情况不用再试

## 核心重点
- N 皇后用列、两条对角线三个数组判重
- 括号生成靠"左括号可放、右括号要等左>右"剪枝
- 棋盘类回溯用坐标 + visited
- 回溯题关键是画决策树
- 剪枝决定能否过大数据

## 练习/任务
1. 实现 N 皇后，输出解法数
2. 生成 n=3 的所有合法括号
3. 实现单词搜索
4. （选做）解数独

## 今日刷题
- **N 皇后**（LeetCode 第 51 题）：经典回溯。
- **括号生成**（LeetCode 第 22 题）：剪枝条件清晰。
- **单词搜索**（LeetCode 第 79 题）：二维网格 DFS。

## 打卡清单
- [ ] 理解 N 皇后建模
- [ ] 会写括号生成
- [ ] 掌握棋盘类回溯
- [ ] 能独立画决策树
