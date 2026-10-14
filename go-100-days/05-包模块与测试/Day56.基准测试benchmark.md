# Day 56 · 基准测试benchmark

## 今日目标
- 学会写基准测试测量性能
- 理解 b.N 的含义
- 会用 benchstat 对比优化

## 学习内容

### 基准测试函数

函数名 `BenchmarkXxx(b *testing.B)`：

```go
func BenchmarkAdd(b *testing.B) {
    for i := 0; i < b.N; i++ {
        Add(2, 3)
    }
}
```

`b.N` 是框架自动决定的循环次数，保证跑够时间。

### 运行基准测试

```powershell
go test -bench . -benchmem
```

输出示例：

```
BenchmarkAdd-8   1000000000   1.23 ns/op   0 B/op
```

含义：每次 Add 耗时 1.23 纳秒、0 次内存分配。

### 性能测试纪律

- 被测代码要放在循环内，别在循环外做预处理
- 用 `b.ResetTimer()` 跳过预处理时间
- `-benchmem` 看内存分配
- 单次结果有噪声，多跑几次或用 benchstat

### 对比 strings.Builder vs +

```go
func BenchmarkConcatPlus(b *testing.B) {
    for i := 0; i < b.N; i++ {
        s := ""
        for j := 0; j < 100; j++ {
            s += "a"
        }
    }
}

func BenchmarkConcatBuilder(b *testing.B) {
    for i := 0; i < b.N; i++ {
        var sb strings.Builder
        for j := 0; j < 100; j++ {
            sb.WriteString("a")
        }
        _ = sb.String()
    }
}
```

跑一遍就知道 Builder 快多少。

## 核心重点
- `BenchmarkXxx(b *testing.B)`，循环跑 b.N 次
- `go test -bench . -benchmem` 运行
- ns/op 每次耗时，B/op 每次分配
- 被测代码放循环内，预处理用 ResetTimer
- 性能优化前先 benchmark 测量

## 练习/任务
1. 给一个函数写基准测试
2. 对比 strings.Builder 与 + 拼接
3. 对比 atomic 与 Mutex 计数器性能
4. 用 -benchmem 看内存分配

## 今日刷题

### 1. LeetCode 108 将有序数组转 BST（复习）
- 思路：中点递归。
- 代码提示：分治。
- Go 要点：平衡树。

### 2. LeetCode 101 对称二叉树（复习）
- 思路：镜像。
- 代码提示：成对。
- Go 要点：树。

## 打卡清单
- [ ] 会写基准测试
- [ ] 会读 ns/op 与 B/op
- [ ] 对比过两种实现性能
- [ ] 复习 BST 与对称树
