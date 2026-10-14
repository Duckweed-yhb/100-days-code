# Day 57 · 示例测试与覆盖率

## 今日目标
- 学会写 Example 测试（兼作文档）
- 学会查看测试覆盖率
- 理解覆盖率不是越高越好

## 学习内容

### Example 测试

函数名 `ExampleXxx`，输出注释 `// Output:` 会被框架校验：

```go
func ExampleAdd() {
    fmt.Println(Add(2, 3))
    // Output: 5
}
```

运行 `go test` 时，框架执行这个函数并对比输出。它既是测试又是 godoc 文档示例。

### 测试覆盖率

```powershell
go test -cover ./...
```

输出覆盖率百分比。生成可视化报告：

```powershell
go test -coverprofile=cover.out
go tool cover -html=cover.out
```

浏览器打开会显示哪些行被覆盖、哪些没覆盖。

### 覆盖率的坑

- 100% 覆盖不代表没有 bug，只代表这些行跑到过。
- 追求 100% 会逼你写无意义的测试，重点测核心逻辑。
- 分支覆盖比行覆盖更有意义。

### TestMain（少见）

需要在所有测试前做初始化时用：

```go
func TestMain(m *testing.M) {
    // 启动测试前准备
    code := m.Run()
    // 清理
    os.Exit(code)
}
```

## 核心重点
- Example 测试兼作文档，Output 注释校验输出
- `go test -cover` 看覆盖率
- `-coverprofile` + `-html` 看可视化报告
- 覆盖率是工具不是目标
- 重点覆盖核心分支与错误路径

## 练习/任务
1. 给 Add 写一个 Example 测试
2. 跑 `go test -cover` 看覆盖率
3. 生成 HTML 覆盖率报告
4. 故意让一段错误分支没测到，看红色标记

## 今日刷题

### 1. LeetCode 121 买卖股票（复习）
- 思路：最低价。
- 代码提示：一次遍历。
- Go 要点：DP。

### 2. LeetCode 53 最大子数组和（复习）
- 思路：Kadane。
- 代码提示：滚动。
- Go 要点：DP。

## 打卡清单
- [ ] 会写 Example 测试
- [ ] 会看覆盖率报告
- [ ] 理解覆盖率不是越高越好
- [ ] 复习股票与子数组
