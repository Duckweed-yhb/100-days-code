# Day 76 · 动态规划入门

## 今日目标
- 理解动态规划（DP）思想
- 掌握 DP 三要素：状态、转移、初始
- 用 DP 优化 Fibonacci

## 学习内容

### DP 是什么

动态规划把大问题拆成重叠子问题，**每个子问题只算一次**，存下来用。区别于分治（子问题不重叠）。

### 从递归 Fibonacci 说起

朴素递归 `fib(n) = fib(n-1) + fib(n-2)`，有大量重复计算：

```
fib(5)
├─ fib(4)
│  ├─ fib(3)
│  └─ fib(2)
└─ fib(3)     ← 重复算
   ├─ fib(2)
   └─ fib(1)
```

O(2ⁿ)。

### DP 优化：记忆化

算过的存起来，下次直接用：

```c
int memo[100];

int fib(int n) {
    if (n <= 1) return n;
    if (memo[n] != -1) return memo[n];
    memo[n] = fib(n-1) + fib(n-2);
    return memo[n];
}

// main 里初始化 memset(memo, -1, sizeof(memo));
```

O(n)。

### 自底向上 DP

从最小子问题往上推：

```c
int fib[100];
fib[0] = 0;
fib[1] = 1;
for (int i = 2; i <= n; i++) {
    fib[i] = fib[i-1] + fib[i-2];
}
```

### DP 三要素

1. **状态定义**：dp[i] 代表什么
2. **转移方程**：dp[i] 怎么从前面推出
3. **初始条件**：dp[0]、dp[1] 是多少

### DP 解题步骤

1. 定义状态
2. 想清楚转移方程
3. 确定遍历顺序
4. 初始化
5. 返回答案

## 核心重点
- DP = 记忆化 + 自底向上
- 子问题重叠是 DP 适用条件
- 三要素：状态、转移、初始
- 避免重复计算
- 从 Fibonacci 开始建立直觉

## 练习/任务
1. 用记忆化递归写 Fibonacci
2. 用自底向上 DP 写 Fibonacci
3. 求 stairs：爬 n 阶楼梯，每次 1 或 2 步，有多少种走法
4. （选做）用滚动变量把 Fibonacci 空间优化到 O(1)

## 今日刷题
- **斐波那契数**（LeetCode 第 509 题）：DP 入门。
- **爬楼梯**（LeetCode 第 70 题）：dp[i] = dp[i-1] + dp[i-2]。
- **打家劫舍**（LeetCode 第 198 题）：dp[i] = max(dp[i-1], dp[i-2]+nums[i])。

## 打卡清单
- [ ] 理解 DP 与分治区别
- [ ] 会写记忆化递归
- [ ] 会写自底向上 DP
- [ ] 掌握三要素
