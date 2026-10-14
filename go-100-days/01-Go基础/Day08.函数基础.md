# Day 08 · 函数基础

## 今日目标
- 掌握函数定义、参数、返回值
- 理解值传递
- 学会使用命名返回值与 blank 返回

## 学习内容

### 函数定义

```go
func add(a int, b int) int {
    return a + b
}

// 相邻同类型参数可合并
func add2(a, b int) int {
    return a + b
}
```

调用：

```go
sum := add(3, 4)
```

### 多返回值

Go 函数可以返回多个值，这是错误处理的基础：

```go
func div(a, b float64) (float64, error) {
    if b == 0 {
        return 0, fmt.Errorf("除数为零")
    }
    return a / b, nil
}

result, err := div(10, 3)
if err != nil {
    fmt.Println("错误:", err)
}
```

### 命名返回值

返回值起名字，函数里直接 `return;` 裸返回：

```go
func swap(a, b int) (first, second int) {
    first = b
    second = a
    return        // 裸返回，自动带上 first、second
}
```

小函数用命名返回值清晰，大函数慎用裸返回。

### 值传递

Go 永远是**值传递**：传参时复制一份。传大结构体时性能差，所以常传指针（Day09 学）。

### 函数作用域

函数内声明的变量外面访问不到；同名变量内层会遮蔽外层。

## 核心重点
- `func 名字(参数列表) 返回类型 { ... }`
- 多返回值是 Go 惯例，第二个常是 error
- 命名返回值可裸返回，小函数用
- Go 全是值传递，传大对象用指针
- main 函数不接收参数、不返回值

## 练习/任务
1. 写一个 `max(a, b int) int` 返回较大值
2. 写一个 `divmod(a, b int) (int, int)` 返回商和余数
3. 写一个函数接收两个整数，交换它们并命名返回
4. 调用 divmod，用 `_` 丢弃其中一个返回值

## 打卡清单
- [ ] 会定义单返回值与多返回值函数
- [ ] 会写命名返回值与裸返回
- [ ] 理解 Go 是值传递
- [ ] 会用 `_` 忽略不需要的返回值
