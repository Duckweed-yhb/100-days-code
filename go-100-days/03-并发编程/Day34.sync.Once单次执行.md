# Day 34 · sync.Once单次执行

## 今日目标
- 掌握 sync.Once 保证某段代码只执行一次
- 理解单例、延迟初始化的典型用途
- 避免重复初始化开销

## 学习内容

### sync.Once

`once.Do(f)` 无论被调用多少次，`f` 只执行一次，且并发安全：

```go
var once sync.Once
var instance *DB

func GetDB() *DB {
    once.Do(func() {
        instance = &DB{conn: "open"}
    })
    return instance
}
```

多个 goroutine 同时调 `GetDB()`，只有一个会真正初始化，其余阻塞等待结果。

### 典型用途

- 单例对象（数据库连接、配置加载）
- 延迟初始化（第一次用到才建）
- 全局资源只打开一次

### 错误做法对比

不用 Once 自己加锁也能做，但容易写错：

```go
// 错误示范：双重检查容易有可见性问题
if instance == nil {
    mu.Lock()
    if instance == nil {
        instance = &DB{}
    }
    mu.Unlock()
}
```

直接用 `sync.Once` 最省心。

### 注意

- `Do` 的函数签名是 `func()`，不能返回错误；初始化失败要自己处理。
- Once 不能复制（复制后变成新的 Once）。

## 核心重点
- `once.Do(f)` 保证 f 全局只跑一次
- 并发安全，多个 goroutine 调用只初始化一次
- 用于单例、延迟初始化
- 不要自己手写双重检查，用 sync.Once
- Once 实例不要按值复制

## 练习/任务
1. 写一个全局配置，用 sync.Once 延迟加载
2. 并发启动 100 个 goroutine 调用 GetDB，确认只初始化一次（加打印）
3. 用 Once 实现一个只打印一次的日志初始化
4. 对比：不用 Once 时初始化函数被调了几次

## 今日刷题

### 1. LeetCode 112 路径总和
- 思路：递归，每深入一层减当前节点值，叶子时判断是否为 0。
- 代码提示：`if root.Left==nil && root.Right==nil { return root.Val==sum }`。
- Go 要点：空节点与叶子节点边界。

### 2. LeetCode 14 二叉树直径（经典题）
- 思路：后序遍历，每个节点算左高+右高，全局更新最大值。
- 代码提示：辅助函数返回高度，副作用更新 maxDiameter。
- Go 要点：用包级变量或指针传最大值。

## 打卡清单
- [ ] 会用 sync.Once 做单例
- [ ] 理解并发只初始化一次
- [ ] 知道何时用 Once
- [ ] 写完路径总和与直径题
