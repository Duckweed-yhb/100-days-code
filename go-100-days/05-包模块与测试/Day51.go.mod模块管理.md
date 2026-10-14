# Day 51 · go.mod模块管理

## 今日目标
- 理解 Go Module 是依赖管理的官方方案
- 学会 go mod init / tidy / build
- 看懂 go.mod 文件

## 学习内容

### 为什么需要模块

Go 1.11 之前用 GOPATH，路径混乱。Module 从 1.16 成为默认方案，每个项目独立管理依赖。

### 初始化模块

```powershell
mkdir myproject
cd myproject
go mod init example.com/myproject
```

生成 `go.mod`：

```
module example.com/myproject

go 1.24
```

### go.mod 文件

```
module example.com/myproject      # 模块路径

go 1.24                           # Go 版本

require (                         # 直接依赖
    github.com/gin-gonic/gin v1.9.1
)
```

### 常用命令

| 命令 | 作用 |
|---|---|
| `go mod init 名` | 初始化模块 |
| `go get pkg@版本` | 拉取依赖 |
| `go mod tidy` | 整理依赖（加缺失、删多余） |
| `go build ./...` | 编译所有包 |
| `go list -m all` | 列出所有依赖 |

### 引入第三方包

```powershell
go get github.com/google/uuid
```

go.mod 会自动加上 require，同时生成 `go.sum` 记录每个依赖的哈希。

## 核心重点
- Module 是 Go 官方依赖方案
- go.mod 记录模块名与依赖
- `go mod tidy` 是整理依赖的万金油
- go.sum 锁定依赖哈希，防篡改
- 模块名通常用仓库地址

## 练习/任务
1. 在新目录 `go mod init` 一个模块
2. 引入 `github.com/google/uuid`，生成一个 UUID 打印
3. 阅读 go.mod 与 go.sum
4. 运行 `go mod tidy` 看依赖被整理

## 今日刷题

### 1. LeetCode 121 买卖股票（复习）
- 思路：维护最低价。
- 代码提示：一次遍历。
- Go 要点：一维 DP。

### 2. LeetCode 53 最大子数组和（复习）
- 思路：Kadane。
- 代码提示：curr = max(n, curr+n)。
- Go 要点：动态规划。

## 打卡清单
- [ ] 会 go mod init / tidy / get
- [ ] 能看懂 go.mod
- [ ] 会引入第三方包
- [ ] 复习股票与子数组
