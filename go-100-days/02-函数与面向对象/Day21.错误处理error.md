# Day 21 · 错误处理error

## 今日目标
- 掌握 Go 错误处理惯例：返回 error
- 学会 errors.New 与 fmt.Errorf
- 正确判断、包装、打印错误

## 学习内容

### error 接口

Go 的错误就是一个内置接口：

```go
type error interface {
    Error() string
}
```

任何实现了 `Error() string` 的类型都是 error。

### 返回 error 的惯用写法

```go
func divide(a, b float64) (float64, error) {
    if b == 0 {
        return 0, errors.New("除数不能为零")
    }
    return a / b, nil
}

result, err := divide(10, 0)
if err != nil {
    fmt.Println("出错:", err)
    return
}
fmt.Println("结果:", result)
```

**规则：err 不为 nil 就是出错，先处理错误再用结果。**

### 创建错误

```go
import "errors"
errors.New("文件不存在")

fmt.Errorf("读取 %s 失败: %v", path, err)   // 带格式化
```

### 不要滥用 panic

`panic` 用于真正不可恢复的程序错误（数组越界、空指针），业务失败一律返回 error。

### 错误处理纪律

- 每个可能的 error 都要检查，别 `_ = err` 忽略
- 错误信息小写开头，不换行
- 向上返回时附加上下文（Day23 学包装）

## 核心重点
- error 是内置接口，失败函数返回 `(结果, error)`
- 用 `errors.New` 或 `fmt.Errorf` 建错误
- `if err != nil { return ... }` 是固定套路
- 业务错误别用 panic
- 不要忽略 error

## 练习/任务
1. 写一个 `parsePositive(s string) (int, error)`，s 不是正整数时返回错误
2. 在 main 里调用它，分别传 "abc"、"-3"、"42" 看错误处理
3. 用 fmt.Errorf 给错误加上下文："年龄无效: ..."
4. 写一个函数故意返回 nil error，确认正常路径

## 打卡清单
- [ ] 会写返回 error 的函数
- [ ] 会用 errors.New / fmt.Errorf
- [ ] 养成 if err != nil 先处理的习惯
- [ ] 知道业务错误不用 panic
