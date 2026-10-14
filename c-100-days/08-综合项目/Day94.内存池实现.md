# Day 94 · 内存池实现

## 今日目标
- 理解为什么需要内存池
- 实现一个简单定长内存池
- 对比 malloc/free 性能

## 学习内容

### 为什么需要内存池

频繁 malloc/free 小块内存慢，还碎片。内存池：一次性从系统申请一大块，自己管理分配释放。

### 定长内存池思路

预先分配 N 个定长块，用空闲链表管理：

```c
#define BLOCK_SIZE 64
#define POOL_SIZE  1000

typedef struct Block {
    struct Block *next;
} Block;

typedef struct {
    char  memory[POOL_SIZE * BLOCK_SIZE];
    Block *free_list;
} MemPool;

void pool_init(MemPool *pool) {
    pool->free_list = NULL;
    for (int i = 0; i < POOL_SIZE; i++) {
        Block *b = (Block*)&pool->memory[i * BLOCK_SIZE];
        b->next = pool->free_list;
        pool->free_list = b;
    }
}

void *pool_alloc(MemPool *pool) {
    if (!pool->free_list) return NULL;
    Block *b = pool->free_list;
    pool->free_list = b->next;
    return b;
}

void pool_free(MemPool *pool, void *p) {
    Block *b = p;
    b->next = pool->free_list;
    pool->free_list = b;
}
```

### 优点

- 分配释放 O(1)
- 无碎片
- 缓存友好

### 缺点

- 定长，不够灵活
- 池大小固定

## 核心重点
- 内存池预分配大块
- 空闲链表管理
- alloc/free O(1)
- 减少碎片
- 适合固定大小对象频繁分配

## 练习/任务
1. 实现定长内存池
2. 分配 100 个块再释放
3. 对比 malloc 与内存池速度
4. （选做）实现变长内存池

## 今日刷题
- 今天项目为主，不安排算法题。

## 打卡清单
- [ ] 理解内存池原理
- [ ] 实现空闲链表
- [ ] 知道为什么快
- [ ] 理解 trade-off
