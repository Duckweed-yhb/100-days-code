# Day 83 · 并查集

## 今日目标
- 理解并查集用途
- 实现 find 与 union
- 掌握路径压缩与按秩合并

## 学习内容

### 并查集是什么

管理一组不相交集合，支持两个操作：
- find(x)：x 属于哪个集合
- union(x, y)：把 x、y 所在集合合并

### 数组表示

```c
int parent[N];

void init(int n) {
    for (int i = 0; i < n; i++) parent[i] = i;
}

int find(int x) {
    if (parent[x] != x) parent[x] = find(parent[x]);  // 路径压缩
    return parent[x];
}

void unite(int x, int y) {
    int rx = find(x), ry = find(y);
    if (rx != ry) parent[rx] = ry;
}
```

### 路径压缩

find 时把路径上所有节点直接挂到根，下次查 O(1)。

### 应用

- 判断两个点是否连通
- Kruskal MST 判环
- 朋友圈个数
- 连通分量数

### 复杂度

路径压缩后几乎 O(1)（反 Ackermann）。

## 核心重点
- 并查集管不相交集合
- find 查根，union 合并
- 路径压缩加速
- 判连通、Kruskal 用
- 实现极简，用途广泛

## 练习/任务
1. 实现并查集
2. 用它判断两个点是否连通
3. 数朋友圈个数（每人朋友关系并起来）
4. （选做）按秩合并优化

## 今日刷题
- **省份数量**（LeetCode 第 547 题）：并查集数连通分量。
- **冗余连接**（LeetCode 第 684 题）：Kruskal 思想。
- **账户合并**（LeetCode 第 721 题）：并查集 + 哈希。

## 打卡清单
- [ ] 实现并查集
- [ ] 会路径压缩
- [ ] 知道并查集应用
- [ ] 能判断连通性
