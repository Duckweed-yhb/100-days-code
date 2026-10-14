# Day 81 · Dijkstra最短路

## 今日目标
- 理解单源最短路问题
- 掌握 Dijkstra 算法
- 知道适用条件（非负权）

## 学习内容

### 问题

给带权有向图，求从起点 s 到所有其他点的最短距离。

### Dijkstra 思想

1. dist[s] = 0，其他 = 无穷
2. 从未确定点中选 dist 最小的 u，标记
3. 用 u 松弛邻居：`dist[v] = min(dist[v], dist[u] + w(u,v))`
4. 重复

### 伪代码

```c
int dist[N];
int visited[N];

for (int i = 0; i < n; i++) dist[i] = INF;
dist[start] = 0;

for (int k = 0; k < n; k++) {
    // 找未确定中 dist 最小的 u
    int u = -1, min_d = INF;
    for (int i = 0; i < n; i++) {
        if (!visited[i] && dist[i] < min_d) {
            min_d = dist[i];
            u = i;
        }
    }
    if (u == -1) break;
    visited[u] = 1;
    // 松弛邻居
    for (Edge *e = graph[u]; e; e = e->next) {
        if (dist[u] + e->w < dist[e->to]) {
            dist[e->to] = dist[u] + e->w;
        }
    }
}
```

O(n²)。用堆优化到 O((V+E) log V)。

### 适用条件

**边权非负**。有负权边要用 Bellman-Ford。

## 核心重点
- Dijkstra 单源最短路
- 每次选最近未确定点
- 松弛邻居
- 要求边权非负
- 堆优化版工程常用

## 练习/任务
1. 手算 Dijkstra 在一个小图上
2. 用邻接矩阵实现 Dijkstra O(n²)
3. 思考为什么不能有负权边
4. （选做）用最小堆优化

## 今日刷题
- **网络延迟时间**（LeetCode 第 743 题）：Dijkstra 模板。
- **最小花费**：地图类最短路问题。

## 打卡清单
- [ ] 理解 Dijkstra 思想
- [ ] 能手写 O(n²) 版
- [ ] 知道非负权限制
- [ ] 会松弛操作
