# Day 15 · 函数一等公民与闭包

## 今日目标
- 理解函数是一等公民，可以赋值、传参、当返回值
- 掌握闭包的概念与典型用途
- 学会用闭包写计数器、函数工厂

## 学习内容

### 函数是值

```go
add := func(a, b int) int { return a + b }   // 匿名函数赋值给变量
fmt.Println(add(2, 3))                        // 5
```

### 函数作参数

```go
func compute(nums []int, f func(int) int) []int {
    out := make([]int, len(nums))
    for i, n := range nums {
        out[i] = f(n)
    }
    return out
}

compute([]int{1, 2, 3}, func(x int) int { return x * x })
// [1 4 9]
```

### 闭包：函数捕获外部变量

闭包 = 函数 + 它引用的外部变量环境。

```go
func counter() func() int {
    count := 0
    return func() int {
        count++          // 捕获了外层的 count
        return count
    }
}

c := counter()
fmt.Println(c())   // 1
fmt.Println(c())   // 2
fmt.Println(c())   // 3
```

每次调用 `counter()` 都新建一个独立 `count`，两个计数器互不干扰。

### 典型用途：函数工厂

```go
func makePrefixer(prefix string) func(string) string {
    return func(s string) string {
        return prefix + ": " + s
    }
}

hi := makePrefixer("Hi")
fmt.Println(hi("Bob"))   // "Hi: Bob"
```

### 注意点

闭包捕获的是**变量本身**，不是当时的值。循环里捕获循环变量是经典坑（Day37 会再讲）。

## 核心重点
- 匿名函数可以赋值、传参、当返回值
- 闭包能记住并修改定义时的外层变量
- 每次调用外层函数产生独立闭包实例
- 闭包适合做计数器、装饰器、函数工厂
- 循环变量被闭包共享是常见坑，需要小心

## 练习/任务
1. 写一个 `Adder()` 返回一个闭包，每次调用累加并返回新和
2. 写一个 `Apply(nums []int, f func(int) int) []int` 对每个元素做变换
3. 用闭包实现一个 `makeIntStream()` 返回从 1 开始递增的序列生成器
4. 写一个 `filter(nums []int, f func(int) bool) []int` 按条件筛选

## 打卡清单
- [ ] 会写匿名函数并赋值
- [ ] 理解闭包如何捕获外层变量
- [ ] 会写函数工厂
- [ ] 能写出 map/filter 风格的高阶函数
