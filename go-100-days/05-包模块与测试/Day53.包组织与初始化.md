# Day 53 · 包组织与初始化

## 今日目标
- 学会在真实项目里组织多包目录
- 理解 init 函数的执行顺序
- 区分可执行包与库包

## 学习内容

### 典型项目结构

```
myproject/
  go.mod
  cmd/
    server/
      main.go       package main
  internal/          私有代码，外部 import 不了
    user/
      user.go       package user
  pkg/               可被外部引用的库
  go.sum
```

### internal 目录

`internal/` 下的包只能被本模块内代码导入，外部模块 import 不到——这是 Go 的封装约定。

### 可执行 vs 库

- `package main` + `func main()` → 编译出可执行文件
- 其他包名 → 库，被 import

### init 函数执行顺序

每个包可以有多个 init()：

1. 先按依赖顺序初始化被导入的包
2. 包内变量先初始化
3. 再执行 init()
4. 最后 main 包的 main()

```go
func init() {
    fmt.Println("user 包初始化")
}
```

### 包设计原则

- 一个包一件事，按业务领域分（user、order、payment），不按技术分层（controller/service）当唯一维度
- 包名短、单数、小写
- 导出最小化

## 核心重点
- cmd/ 放入口，internal/ 放私有，pkg/ 放可复用库
- internal 外部 import 不到
- package main 才有 main 函数
- init 自动执行，少用
- 包按业务领域组织

## 练习/任务
1. 建一个有 cmd/server 和 internal/user 的项目
2. 在 user 包写一个导出函数，在 main 调用
3. 写一个 init() 看执行顺序
4. 尝试从模块外 import internal 包，看报错

## 今日刷题

### 1. LeetCode 101 对称二叉树（复习）
- 思路：镜像递归。
- 代码提示：成对比较。
- Go 要点：树。

### 2. LeetCode 104 二叉树最大深度（复习）
- 思路：递归。
- 代码提示：1 + max(left, right)。
- Go 要点：树。

## 打卡清单
- [ ] 会组织 cmd/internal/pkg 结构
- [ ] 理解 internal 的私有性
- [ ] 知道 init 执行顺序
- [ ] 复习两道树题
