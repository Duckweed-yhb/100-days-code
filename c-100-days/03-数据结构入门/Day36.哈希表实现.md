# Day 36 · 哈希表实现

## 今日目标
- 理解哈希表"用空间换时间"的核心思想
- 掌握哈希函数、冲突解决
- 实现一个简易哈希表

## 学习内容

### 为什么需要哈希表

数组按下标访问 O(1)，但按下标 = 知道索引。如果 key 是字符串（如姓名），怎么把它变成下标？哈希表就是干这件事的。

### 哈希函数

把任意 key 映射成一个数组下标：

```c
int hash(const char *key, int table_size) {
    unsigned long h = 0;
    while (*key) {
        h = h * 31 + *key++;     // 经典字符串哈希
    }
    return h % table_size;
}
```

好的哈希函数让 key 均匀分布到不同槽位。

### 冲突

不同 key 可能哈希到同一个槽位，这叫冲突。两种解决办法：

**1. 链地址法**：每个槽位挂一个链表，冲突的都链在后面：

```c
typedef struct Entry {
    char key[32];
    int  value;
    struct Entry *next;
} Entry;

Entry *table[100];   // 哈希表
```

**2. 开放地址法**：冲突了就找下一个空槽（线性探测）。

今天只实现链地址法。

### 插入

```c
void hash_put(const char *key, int value) {
    int idx = hash(key, 100);
    // 先查 key 是否已存在，存在就更新
    for (Entry *e = table[idx]; e; e = e->next) {
        if (strcmp(e->key, key) == 0) {
            e->value = value;
            return;
        }
    }
    // 不存在，头插新节点
    Entry *e = malloc(sizeof(Entry));
    strcpy(e->key, key);
    e->value = value;
    e->next = table[idx];
    table[idx] = e;
}
```

### 查找

```c
int hash_get(const char *key, int *out) {
    int idx = hash(key, 100);
    for (Entry *e = table[idx]; e; e = e->next) {
        if (strcmp(e->key, key) == 0) {
            *out = e->value;
            return 1;
        }
    }
    return 0;
}
```

### 平均复杂度

哈希函数均匀时，查找插入删除平均 O(1)。最坏情况所有 key 都冲突，退化成链表 O(n)。

### 负载因子

`元素数 / 槽位数`。负载因子太高冲突变多，性能下降。生产哈希表会自动扩容（rehash）。

## 核心重点
- 哈希表把 key 映射成下标，平均 O(1) 查找
- 哈希函数要均匀
- 冲突用链地址法（链表）或开放地址法解决
- 负载因子太高要扩容
- 哈希表是 O(1) 查找的利器

## 练习/任务
1. 实现字符串哈希函数
2. 实现哈希表的 put 和 get（链地址法）
3. 插入 10 个键值对，然后按 key 查询
4. （选做）实现删除操作

## 今日刷题
- **两数之和**（LeetCode 第 1 题）：边遍历边查哈希表，O(n)。
- **存在重复元素**（LeetCode 第 217 题）：哈希表存见过的元素。
- **字母异位词分组**（LeetCode 第 49 题）：排序后的字符串作 key 分组。

## 打卡清单
- [ ] 理解哈希表 O(1) 的原理
- [ ] 会写简单哈希函数
- [ ] 实现链地址法哈希表
- [ ] 知道冲突与负载因子
