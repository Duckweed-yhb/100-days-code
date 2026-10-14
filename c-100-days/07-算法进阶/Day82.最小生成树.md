# Day 82 · 最小生成树

## 今日目标
- 理解最小生成树 MST
- 掌握 Prim 与 Kruskal
- 知道区别与选择

## 学习内容

### 什么是 MST

连通无向图，选 n-1 条边把所有点连起来，总权最小。

### Kruskal 算法

1. 所有边按权排序
2. 从小到大取边，如果不形成环就加入
3. 取满 n-1 条边停

判断成环用并查集（明天学）。

```c
// 边排序后
sort(edges, edges+m, by_weight);
for (每条边 e) {
    if (find(e.u) != find(e.v)) {
        unite(e.u, e.v);
        total += e.w;
    }
}
```

O(E log E)，适合稀疏图。

### Prim 算法

类似 Dijkstra：
1. 从一个点开始
2. 每次选连接树内外的最短边
3. 把新点加进树

O(V²) 或堆优化 O(E log V)，适合稠密图。

### MST 应用

- 布线：最小成本连所有城市
- 聚类：MST 砍最长边就是两类

## 核心重点
- MST：n 点 n-1 边，总权最小
- Kruskal：边排序 + 并查集
- Prim：点扩展，类似 Dijkstra
- Kruskal 稀疏图快，Prim 稠密图快
- 不形成环是关键

## 练习/任务
1. 手算 Kruskal 在一个小图上
2. 实现 Kruskal
3. 理解 Prim 思想
4. （选做）实现 Prim

## 今日刷题
- **连接所有点的最小费用**（LeetCode 第 1584 题）：MST 直接应用。
- 今天概念为主，复习 Dijkstra。

## 打卡清单
- [ ] 理解 MST 问题
- [ ] 会写 Kruskal
- [ ] 知道两种算法区别
- [ ] 理解不形成环
