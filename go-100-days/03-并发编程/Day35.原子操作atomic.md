# Day 35 · 原子操作atomic

## 今日目标
- 理解原子操作与锁的取舍
- 掌握 sync/atomic 常用类型
- 知道什么时候该用 atomic 而不是 Mutex

## 学习内容

### 为什么需要原子操作

计数器 `counter++` 是"读-改-写"三步，多 goroutine 交错会丢更新。Mutex 能解决，但对简单计数器太重。`sync/atomic` 提供硬件级原子操作，更快更轻。

### 常用原子类型

Go 1.19 后推荐用 `atomic.Int64`、`atomic.Bool` 等类型：

```go
import "sync/atomic"

var counter atomic.Int64

func worker() {
    for i := 0; i < 1000; i++ {
        counter.Add(1)      // 原子加
    }
}

// 主 goroutine 读完
fmt.Println(counter.Load())   // 原子读
counter.Store(0)              // 原子写
```

### 对比 Mutex

```go
// Mutex 版本
mu.Lock()
counter++
mu.Unlock()

// atomic 版本
counter.Add(1)
```

简单计数器用 atomic 更简洁高效；复杂多字段更新仍需 Mutex。

### 原子指针（进阶）

`atomic.Pointer[T]` 可原子替换整个对象，常用于无锁读配置。

### 选型建议

| 场景 | 选择 |
|---|---|
| 单个 int/bool 计数、标志 | atomic |
| 多个字段要一起改一致 | Mutex |
| 复杂临界区 | Mutex |
| 传递事件/数据 | channel |

## 核心重点
- atomic 提供无锁原子操作，比 Mutex 轻量
- `atomic.Int64` 的 Add/Load/Store
- 单变量计数用 atomic，多变量一致用 Mutex
- 不要把 atomic 当万能，复杂逻辑还是锁
- `go run -race` 同样能检测 atomic 用法

## 练习/任务
1. 用 atomic.Int64 实现 Day30 的计数器，验证结果正确
2. 用 atomic.Bool 做一个"已停止"标志，控制 goroutine 退出
3. 对比 atomic 与 Mutex 版本的性能（Day56 基准测试）
4. 写一个原子递增 ID 生成器

## 今日刷题

### 1. LeetCode 108 将有序数组转二叉搜索树
- 思路：中点作根，递归左右两半。
- 代码提示：`mid := (lo+hi)/2; root := &TreeNode{Val:nums[mid]}`。
- Go 要点：分治递归。

### 2. LeetCode 889 根据前序后序构造二叉树（进阶）
- 思路：前序首是根，后序倒数第二是右子树根，递归划分。
- 代码提示：用 map 存后序值→下标加速划分。
- Go 要点：理解三种遍历构造树的关系。

## 打卡清单
- [ ] 会用 atomic.Int64/Bool
- [ ] 知道 atomic 与 Mutex 取舍
- [ ] 会写原子计数器
- [ ] 写完有序数组转平衡 BST
