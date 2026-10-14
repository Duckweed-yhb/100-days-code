# Day 19 · 类型断言与类型分支

## 今日目标
- 学会从接口值中取出具体类型
- 掌握 type switch
- 避开接口 nil 相关的常见坑

## 学习内容

### 类型断言

接口值装的是具体类型，要拿出来用：

```go
var s any = "hello"

str := s.(string)        // 断言成 string
fmt.Println(str)

// 不安全形式：断言失败会 panic
// n := s.(int)           // panic!
```

### 安全形式：带 ok

```go
str, ok := s.(string)
if ok {
    fmt.Println("是字符串:", str)
} else {
    fmt.Println("不是字符串")
}
```

永远用这种形式，不要裸断言。

### type switch 按类型分支

```go
func classify(v any) {
    switch x := v.(type) {
    case int:
        fmt.Println("整数:", x)
    case string:
        fmt.Println("字符串:", x)
    case nil:
        fmt.Println("nil")
    default:
        fmt.Println("未知类型")
    }
}

classify(42)
classify("hi")
classify(true)
```

### 常见坑：接口里装了 nil 指针

```go
func returnsError() error {
    var p *MyError = nil
    return p            // 返回的 error 接口不是 nil！
}
```

接口值要同时 (类型, 值) 都为 nil 才等于 nil。这是为什么错误处理要小心，Day23 再深入。

## 核心重点
- `v.(T)` 从接口取具体类型，失败 panic
- 用 `v, ok := v.(T)` 安全断言
- `switch v.(type)` 按类型分支
- 空接口 any 里取出值后才能调用具体类型方法
- 接口值 nil 判断要小心"装了 nil 指针"的情况

## 练习/任务
1. 写一个函数接收 any，用 type switch 打印它的真实类型
2. 安全断言一个 any 是 *os.File，失败时打印提示
3. 写一个 `Sum(values ...any) int`，只对其中 int 求和
4. 故意写一次裸断言触发 panic，再改成安全形式

## 打卡清单
- [ ] 会用安全类型断言
- [ ] 会写 type switch
- [ ] 理解接口值的 (类型, 值) 结构
- [ ] 知道裸断言会 panic
