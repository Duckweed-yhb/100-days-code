# Day 14 · 函数多返回值与可变参数

## 今日目标
- 巩固多返回值并掌握其常见模式
- 学会可变参数函数
- 理解 defer 的初步用法（Day22 详讲）

## 学习内容

### 多返回值模式

Go 里最常见的两类多返回值：

```go
// 1) 结果 + 错误
func parseInt(s string) (int, error)

// 2) 值 + 是否存在（map 读取、类型断言）
v, ok := m[key]
```

写新函数时，凡是可能失败的操作都应返回 `(结果, error)`。

### 可变参数

`...类型` 让函数接收 0 到多个同类型参数：

```go
func sum(nums ...int) int {
    total := 0
    for _, n := range nums {
        total += n
    }
    return total
}

sum()          // 0
sum(1, 2)      // 3
sum(1, 2, 3)   // 6
```

可变参数在函数内表现为切片 `[]int`。

### 展开已有切片

用 `...` 把切片拆开传进去：

```go
scores := []int{80, 90, 100}
sum(scores...)      // 等价于 sum(80, 90, 100)
```

### 混合参数

可变参数必须放最后：

```go
func join(sep string, parts ...string) string {
    return strings.Join(parts, sep)
}
join("-", "a", "b", "c")   // "a-b-c"
```

### 函数也是类型

```go
type Op func(int, int) int   // 函数类型

func apply(a, b int, op Op) int {
    return op(a, b)
}
```

这是 Day15 闭包与 Day18 接口的基础。

## 核心重点
- 失败操作返回 `(结果, error)` 是 Go 惯例
- `...T` 声明可变参数，函数内是 `[]T`
- 用 `slice...` 展开已有切片
- 可变参数必须在参数列表末尾
- 函数可以作为参数传递（函数类型）

## 练习/任务
1. 写一个 `max(nums ...int) int` 求任意个数的最大值
2. 写一个 `Concat(sep string, parts ...string) string` 不依赖 strings.Join 自己拼接
3. 写一个 `apply(a, b int, op func(int,int) int) int`，分别传入加法和乘法
4. 对空可变参数调用 sum()，确认返回 0 不报错

## 打卡清单
- [ ] 会写可变参数函数
- [ ] 会用 `...` 展开切片传参
- [ ] 理解函数类型与传函数
- [ ] 熟悉"结果+错误"多返回值模式
