# Day 22 · defer与panic

## 今日目标
- 掌握 defer 的执行时机与用途
- 理解 panic 的作用与风险
- 学会用 defer 做资源清理

## 学习内容

### defer：延迟执行

`defer` 注册一个函数调用，在**当前函数返回前**执行。常用于关闭文件、解锁、关闭连接：

```go
func readFile(path string) error {
    f, err := os.Open(path)
    if err != nil {
        return err
    }
    defer f.Close()        // 函数返回时自动 Close

    // ... 读写文件
    return nil
}
```

### 多个 defer 的顺序

defer 是**后进先出（栈）**：

```go
func main() {
    defer fmt.Println("第一句注册")
    defer fmt.Println("第二句注册")
}
// 输出：
// 第二句注册
// 第一句注册
```

### defer 的参数立即求值

```go
x := 10
defer fmt.Println(x)     // 参数 x 此时就求值为 10
x = 20
// 函数返回时打印 10，不是 20
```

### panic

panic 表示不可恢复的运行时错误，会中断正常执行、开始栈展开：

```go
func risky() {
    panic("炸了")
}
```

日常代码**不要主动 panic**，库函数更不应 panic 给调用方用。

### defer 在 panic 时仍会执行

函数 panic 时，已经 defer 的函数照样会跑，所以资源不会泄漏。

## 核心重点
- `defer f.Close()` 是资源清理标准写法
- 多个 defer 后进先出
- defer 的参数在注册时就求值
- panic 用于真正异常，业务代码不要用
- panic 时 defer 仍会执行

## 练习/任务
1. 打开一个文件，用 defer 关闭，验证函数结束后文件确实关闭
2. 写三个 defer，观察 LIFO 顺序
3. 写一个函数故意 panic，看栈展开时 defer 是否执行
4. 用 defer 给一个 mutex Unlock（Day30 再深入）

## 打卡清单
- [ ] 会用 defer 关闭文件/连接
- [ ] 理解 defer LIFO 顺序
- [ ] 知道 defer 参数立即求值
- [ ] 知道 panic 与 defer 的关系
