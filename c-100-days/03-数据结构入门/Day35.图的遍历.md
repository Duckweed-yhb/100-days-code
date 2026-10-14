# Day 35 · 图的遍历

## 今日目标
- 实现图的 BFS（广度优先）和 DFS（深度优先）
- 理解 visited 数组的作用
- 知道两种遍历的适用场景

## 学习内容

### 为什么需要 visited

图里有环，不像树单向往下走。如果不记录访问过的节点，会在两个节点之间无限循环。

```c
int visited[N];
```

### DFS：深度优先（一条路走到黑）

```c
void dfs(int u) {
    visited[u] = 1;
    printf("访问 %d\n", u);
    // 遍历 u 的所有邻居
    for (Edge *e = graph[u]; e; e = e->next) {
        if (!visited[e->to]) {
            dfs(e->to);
        }
    }
}
```

像走迷宫：沿着一条路一直走，走到头再回头换路。递归实现，本质是栈。

### BFS：广度优先（一层一层扩）

```c
void bfs(int start) {
    int q[N], head = 0, tail = 0;
    q[tail++] = start;
    visited[start] = 1;
    while (head < tail) {
        int u = q[head++];
        printf("访问 %d\n", u);
        for (Edge *e = graph[u]; e; e = e->next) {
            if (!visited[e->to]) {
                visited[e->to] = 1;
                q[tail++] = e->to;
            }
        }
    }
}
```

像水滴涟漪：从起点一圈一圈往外扩。用队列实现。

### BFS vs DFS

| 方面 | DFS | BFS |
|---|---|---|
| 数据结构 | 栈（递归） | 队列 |
| 顺序 | 一条路走到底 | 一层一层扩 |
| 最短路 | 不保证 | 无权图最短路径 |
| 实现 | 递归简洁 | 迭代 |

### 连通分量

从每个未访问节点开始 DFS/BFS，一次 DFS 能访问到的所有节点构成一个连通分量。数一下调用了几次 DFS，就知道图有几个连通分量。

### 应用

- DFS：找路径、拓扑排序、环检测
- BFS：无权图最短路、层次遍历、朋友圈扩散

## 核心重点
- 图遍历必须有 visited 数组防环
- DFS 一条路走到黑，用栈/递归
- BFS 一层一层扩，用队列
- BFS 是无权图最短路的标准算法
- 连通分量数 = DFS 调用次数

## 练习/任务
1. 用邻接表建一张图，分别写 DFS 和 BFS 遍历
2. 数一下图有几个连通分量
3. 用 BFS 求从起点到各点的最短距离（边权为 1）
4. （选做）判断无向图是否有环（DFS 时记录父节点）

## 今日刷题
- **岛屿数量**（LeetCode 第 200 题）：DFS/BFS 染色，每遇到一个 1 就计数并把整个岛淹掉。
- **克隆图**（LeetCode 第 133 题）：DFS 一边拷贝一边建映射。
- **腐烂的橘子**（LeetCode 第 994 题）：多源 BFS，一开始把所有烂橘子入队。

## 打卡清单
- [ ] 理解为什么需要 visited
- [ ] 实现 DFS 和 BFS
- [ ] 知道 BFS 求最短路
- [ ] 能求连通分量个数
