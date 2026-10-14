# Day 84 · KMP字符串匹配

## 今日目标
- 理解字符串匹配问题
- 认识 KMP 为什么比暴力快
- 会写 next 数组与匹配

## 学习内容

### 问题

主串 S 中找模式串 P 出现位置。暴力 O(n*m)，KMP O(n+m)。

### next 数组

next[i] 表示 P[0..i] 的最长相等真前后缀长度。KMP 靠它在失配时不从头来。

### 求 next

```c
int next[M];
void build_next(const char *p) {
    int j = 0;
    for (int i = 1; p[i]; i++) {
        while (j > 0 && p[i] != p[j]) j = next[j-1];
        if (p[i] == p[j]) j++;
        next[i] = j;
    }
}
```

### 匹配

```c
int kmp(const char *s, const char *p) {
    build_next(p);
    int j = 0;
    for (int i = 0; s[i]; i++) {
        while (j > 0 && s[i] != p[j]) j = next[j-1];
        if (s[i] == p[j]) j++;
        if (p[j] == '\0') return i - j + 1;
    }
    return -1;
}
```

### 为什么快

失配时主串 i 不回退，只动 j，所以 O(n+m)。

### 工程

C 里一般直接用库函数 strstr。但 KMP 是面试高频。

## 核心重点
- KMP O(n+m) 字符串匹配
- next 数组存最长相等前后缀
- 失配时用 next 回退 j，i 不回退
- 面试高频，理解思想即可
- strstr 是工程选择

## 练习/任务
1. 手算 "ABABC" 的 next 数组
2. 实现 KMP 匹配
3. 与暴力法对比速度
4. （选做）理解 next 为什么这么求

## 今日刷题
- **实现 strStr**（LeetCode 第 28 题）：KMP 或暴力。
- **重复的子字符串**（LeetCode 第 459 题）：KMP next 数组应用。

## 打卡清单
- [ ] 理解 KMP 思想
- [ ] 会写 next 数组
- [ ] 知道为什么 O(n+m)
- [ ] 能手写 KMP
