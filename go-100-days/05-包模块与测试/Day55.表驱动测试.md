# Day 55 · 表驱动测试

## 今日目标
- 掌握 Go 最具代表性的表驱动测试
- 学会用 t.Run 分子测试
- 高效覆盖多种输入输出组合

## 学习内容

### 表驱动测试

把多组 (输入, 期望) 放切片里循环跑，是 Go 测试的惯用写法：

```go
func TestAdd(t *testing.T) {
    cases := []struct {
        name string
        a, b int
        want int
    }{
        {"正数相加", 2, 3, 5},
        {"含零", 0, 5, 5},
        {"负数", -1, -2, -3},
    }

    for _, c := range cases {
        t.Run(c.name, func(t *testing.T) {
            got := Add(c.a, c.b)
            if got != c.want {
                t.Errorf("Add(%d,%d) = %d, want %d", c.a, c.b, got, c.want)
            }
        })
    }
}
```

### t.Run 子测试

每个 case 是一个子测试，可以单独跑：

```powershell
go test -v -run TestAdd/正数相加 .
```

### 优点

- 加测试用例只需往切片里加一行
- 报告清晰，哪个 case 挂了一目了然
- 与表格对照阅读方便

### 测试 error 返回

```go
cases = append(cases, struct{...}{"除零", 1, 0, 0})
// 里面判断 err != nil
```

## 核心重点
- 表驱动：切片放多组输入输出
- t.Run(name, func) 分子测试
- 加用例只需加一行
- 子测试可单独运行
- 这是 Go 测试最常用模式

## 练习/任务
1. 把 Day54 的 Add 测试改写成表驱动
2. 给 Div 函数写表驱动测试，含除零用例
3. 单独运行某个子测试
4. 故意让一个 case 失败，看报告

## 今日刷题

### 1. LeetCode 141 环形链表（复习）
- 思路：快慢指针。
- 代码提示：相遇即有环。
- Go 要点：指针。

### 2. LeetCode 142 环形链表 II（复习）
- 思路：找入口。
- 代码提示：同步走。
- Go 要点：链表进阶。

## 打卡清单
- [ ] 会写表驱动测试
- [ ] 会用 t.Run 分子测试
- [ ] 会单独跑子测试
- [ ] 复习环形链表
