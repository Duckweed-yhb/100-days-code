# Day 54 · 单元测试testing

## 今日目标
- 学会写第一个 Go 单元测试
- 掌握 testing.T 的断言方式
- 知道测试文件命名规则

## 学习内容

### 测试文件约定

测试文件与源文件同目录，命名 `xxx_test.go`。要测试 `math.go` 就写 `math_test.go`。

### 第一个测试

```go
// math.go
package mathutil

func Add(a, b int) int {
    return a + b
}
```

```go
// math_test.go
package mathutil

import "testing"

func TestAdd(t *testing.T) {
    got := Add(2, 3)
    want := 5
    if got != want {
        t.Errorf("Add(2,3) = %d, want %d", got, want)
    }
}
```

### 运行测试

```powershell
go test ./...            # 跑所有测试
go test -v ./...         # 详细输出
go test -run TestAdd .   # 只跑某个
```

### testing.T 常用方法

| 方法 | 作用 |
|---|---|
| `t.Errorf` | 打印错误，测试继续 |
| `t.Fatalf` | 打印错误并立即终止当前测试 |
| `t.Log` | 普通日志（-v 才显示） |
| `t.Skip` | 跳过 |

### 没有内置断言

Go 标准库不带 assert，就用 `if got != want { t.Errorf }`。项目里常引 `github.com/stretchr/testify/assert` 简化。

## 核心重点
- 测试文件命名 `xxx_test.go`
- 函数名 `TestXxx(t *testing.T)`
- 用 `if got != want` 判断，t.Errorf 报错
- `go test ./...` 跑全部
- 标准库无 assert，可引入 testify

## 练习/任务
1. 给 Add 函数写一个测试
2. 故意让测试失败，看输出
3. 写一个 Div 函数（除零返回 error），写测试覆盖成功与失败
4. 用 -v 跑测试看详细日志

## 今日刷题

### 1. LeetCode 206 反转链表（复习）
- 思路：三指针。
- 代码提示：prev/cur/next。
- Go 要点：链表。

### 2. LeetCode 94 二叉树中序遍历（复习）
- 思路：递归。
- 代码提示：左根右。
- Go 要点：树。

## 打卡清单
- [ ] 会写并运行单元测试
- [ ] 会用 Errorf/Fatalf
- [ ] 知道测试文件命名规则
- [ ] 复习反转链表与中序遍历
