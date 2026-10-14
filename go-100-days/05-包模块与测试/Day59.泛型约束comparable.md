# Day 59 · 泛型约束comparable

## 今日目标
- 理解类型约束的作用
- 掌握 comparable 与自定义约束
- 学会写有方法要求的泛型

## 学习内容

### 为什么需要约束

`any` 太宽泛——如果函数要比较两个 T 是否相等，`any` 不够，必须要求 T 可比较。

### comparable

`comparable` 约束表示类型支持 `==` 和 `!=`：

```go
func Contains[T comparable](list []T, target T) bool {
    for _, v := range list {
        if v == target {
            return true
        }
    }
    return false
}

Contains([]int{1, 2, 3}, 2)        // true
Contains([]string{"a", "b"}, "c")  // false
```

### 自定义约束

用 interface 定义约束，要求类型实现某些方法：

```go
type Number interface {
    int | float64
}

func Sum[T Number](list []T) T {
    var total T
    for _, v := range list {
        total += v
    }
    return total
}
```

### 多类型约束

```go
type Stringer interface {
    String() string
}

func PrintList[T Stringer](list []T) {
    for _, v := range list {
        fmt.Println(v.String())
    }
}
```

### 实际常用

标准库 `golang.org/x/exp/constraints` 提供 Ordered（支持 <）等，但标准库自带的 `comparable` 已覆盖大部分场景。

## 核心重点
- `any` 任意类型，`comparable` 可比较
- 约束写在 interface 里
- `int | float64` 联合类型限定基础类型
- 约束可要求方法
- 泛型 + 约束让代码既通用又安全

## 练习/任务
1. 写一个泛型 Contains[T comparable]
2. 写一个泛型 Sum[T Number] 支持 int/float64
3. 写一个泛型 Print[T Stringer] 调用 String()
4. 尝试对不支持 == 的类型（如切片）用 Contains，看编译错误

## 今日刷题

### 1. LeetCode 217 存在重复元素（复习）
- 思路：map。
- 代码提示：set 模拟。
- Go 要点：map。

### 2. LeetCode 136 只出现一次的数字（复习）
- 思路：异或。
- 代码提示：位运算。
- Go 要点：位运算。

## 打卡清单
- [ ] 会用 comparable 约束
- [ ] 会写自定义约束
- [ ] 理解类型约束的意义
- [ ] 复习 set 与位运算
