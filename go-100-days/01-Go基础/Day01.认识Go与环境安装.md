# Day 01 · 认识Go与环境安装

## 今日目标
- 搞清 Go 是什么、为什么云原生时代都在用它
- 在本机装好 Go 开发环境（Windows）
- 跑通第一个 Go 程序，理解"编译 → 运行"的完整流程

## 学习内容

### Go 是什么

Go（又称 Golang）由 Google 的 Robert Griesemer、Rob Pike、Ken Thompson 于 2007 年设计，2012 年发布 1.0。它是一门**编译型、静态类型、自带垃圾回收**的系统级语言，语法刻意做得很简单，同时把并发（goroutine）做进了语言层面。

为什么今天学 Go：

| 理由 | 说明 |
|---|---|
| 云原生主力 | Docker、Kubernetes、etcd、Prometheus、Terraform 全是 Go 写的 |
| 并发友好 | 原生 goroutine + channel，写高并发服务比 Java 轻松 |
| 编译部署快 | 编译成单个静态二进制，扔到服务器就能跑，无运行时依赖 |
| 就业方向 | 后端、云原生、基础架构、爬虫、工具链岗位需求旺盛 |

### 安装开发环境（Windows）

1. 打开 Go 官网下载页 https://go.dev/dl/ ，下载 Windows 版安装包（`.msi`，选 amd64）。
2. 双击 `.msi` 一路下一步，默认装到 `C:\Program Files\Go`。
3. 安装程序会自动把 `C:\Program Files\Go\bin` 加入系统 PATH。
4. 推荐编辑器用 **VS Code**，安装官方扩展 **Go**（Google 出品）。

验证安装（PowerShell）：

```powershell
go version
```

能输出 `go version go1.2x.x windows/amd64` 即成功。

### 第一个 Go 程序

Go 1.11 之后用模块（module）管理工程，先建模块再写代码：

```powershell
mkdir hello
cd hello
go mod init hello
```

在 `hello` 目录新建 `main.go`：

```go
package main            // 声明文件属于 main 包（可执行程序入口包）

import "fmt"            // 引入标准库格式化包

func main() {           // main 函数：程序入口，无参数无返回值
    fmt.Println("Hello, World!")   // 打印一行并自动换行
}
```

运行：

```powershell
go run main.go
```

屏幕输出 `Hello, World!` 即成功。也可以先编译再运行：

```powershell
go build               // 生成 hello.exe
.\hello.exe
```

### 理解"编译 → 运行"

Go 是编译型语言：`go run` 是"编译到临时目录并立刻运行"的便利命令；`go build` 则把源代码编译成单个可执行文件。这个 `.exe` 不依赖本机是否装了 Go，拷到别的 Windows 机器也能直接跑——这正是 Go 部署简单的原因。

## 核心重点
- Go 是编译型、静态类型、带 GC、原生支持并发的语言
- 程序入口在 `package main` 包里的 `func main()`
- `fmt.Println` 用于打印一行文本并换行
- 先 `go mod init 模块名` 建模块，再用 `go run` / `go build` 运行
- 安装后用 `go version` 验证

## 练习/任务
1. 安装好 Go，执行 `go version` 确认版本号
2. **手敲**（不要复制粘贴）`main.go`，用 `go run` 跑出 `Hello, World!`
3. 修改程序，打印两行：第一行 `Hello, World!`，第二行你自己的名字
4. （选做）用 `go build` 生成 exe，把它拷到一个新文件夹双击运行

## 打卡清单
- [ ] 已安装 Go，`go version` 有输出版本号
- [ ] 已用 `go mod init` 建好第一个模块并跑通 Hello World
- [ ] 已尝试用 `go build` 生成可执行文件并运行
- [ ] 已理解 Go 编译型语言"编译 → 运行"的基本流程
