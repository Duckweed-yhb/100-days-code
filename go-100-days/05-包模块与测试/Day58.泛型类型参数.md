# Day 58 · 泛型类型参数

## 今日目标
- 理解 Go 1.18 引入的泛型
- 学会写带类型参数的函数
- 学会写带类型参数的结构体

## 学习内容

### 为什么需要泛型

没有泛型时，要对 int 写一个 Map、对 string 再写一个 Map，代码重复。泛型让一份逻辑支持多种类型。

### 泛型函数

```go
func Map[T any, U any](list []T, f func(T) U) []U {
    out := make([]U, len(list))
    for i, v := range list {
        out[i] = f(v)
    }
    return out
}

// 使用
nums := []int{1, 2, 3}
squares := Map(nums, func(x int) int { return x * x })
strs := Map([]string{"a", "b"}, strings.ToUpper)
```

`[T any, U any]` 声明类型参数，`any` 表示任意类型。

### 泛型结构体

```go
type Stack[T any] struct {
    items []T
}

func (s *Stack[T]) Push(v T) {
    s.items = append(s.items, v)
}

func (s *Stack[T]) Pop() T {
    v := s.items[len(s.items)-1]
    s.items = s.items[:len(s.items)-1]
    return v
}
```

使用时指定类型：`Stack[int]`、`Stack[string]`。

### 类型推断

调用时通常不用显式写 `Map[int, int]`，编译器能从实参推断。

## 核心重点
- `[T any]` 声明类型参数
- 泛型一份逻辑支持多类型
- 泛型结构体方法也要带 `[T any]`
- 调用时编译器通常自动推断类型
- 不要为了泛型而泛型，类型真的多变才用

## 练习/任务
1. 写一个泛型 Map 函数处理 []int
2. 写一个泛型 Stack[T] 实现 Push/Pop
3. 写一个泛型 Filter[T] 按条件过滤切片
4. 用泛型写一个 Contains[T comparable] 判断元素是否在切片

## 今日刷题

### 1. LeetCode 70 爬楼梯（复习）
- 思路：DP。
- 代码提示：滚动变量。
- Go 要点：斐波那契。

### 2. LeetCode 118 杨辉三角（复习）
- 思路：递推。
- 代码提示：首尾 1。
- Go 要点：二维切片。

## 打卡清单
- [ ] 会写泛型函数
- [ ] 会写泛型结构体
- [ ] 理解类型参数与类型推断
- [ ] 复习 DP 基础题
