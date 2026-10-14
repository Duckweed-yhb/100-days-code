# Day 23 · recover与错误包装

## 今日目标
- 学会用 recover 捕获 panic
- 掌握 Go 1.20 的错误包装 `%w` 与 errors.Is / As
- 区分错误的"判断类型"与"判断值"

## 学习内容

### recover 捕获 panic

`recover()` 只在 defer 函数里有效，能让程序从 panic 恢复：

```go
func safeRun() {
    defer func() {
        if r := recover(); r != nil {
            fmt.Println("恢复了:", r)
        }
    }()
    panic("炸了")
}
```

Web 服务器常用它在每个请求里 recover，防止一个请求 panic 搞崩整个服务。

### 错误包装（Go 1.20+）

用 `fmt.Errorf` 的 `%w` 把底层错误包进去：

```go
if err != nil {
    return fmt.Errorf("读取配置失败: %w", err)
}
```

### errors.Is：判断是不是某个错误

```go
var ErrNotFound = errors.New("not found")

if errors.Is(err, ErrNotFound) {
    // 不管被包了几层，都能匹配
}
```

### errors.As：取出具体错误类型

```go
var pathErr *fs.PathError
if errors.As(err, &pathErr) {
    fmt.Println("路径:", pathErr.Path)
}
```

### 不要滥用 recover

recover 是最后防线，不是替代错误返回的工具。普通业务错误仍然要用 `if err != nil`。

## 核心重点
- `recover()` 只在 defer 中有效，用于兜底
- `fmt.Errorf("...: %w", err)` 包装错误链
- `errors.Is(err, 目标错误值)` 判断错误链中是否包含
- `errors.As(err, &目标类型指针)` 取出具体类型
- 不要用 recover 替代正常错误处理

## 练习/任务
1. 写一个函数可能 panic，用 defer+recover 保护
2. 定义 `ErrNotFound`，在底层返回它，上层包装后用 errors.Is 判断
3. 用 errors.As 取出一个 *PathError 并打印 Path
4. 写一个 Web 风格的 middleware 风格 recover 函数

## 打卡清单
- [ ] 会在 defer 里 recover
- [ ] 会用 %w 包装错误
- [ ] 会用 errors.Is / errors.As
- [ ] 知道 recover 是兜底不是常规手段
