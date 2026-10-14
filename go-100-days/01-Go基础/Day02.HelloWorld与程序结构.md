# Day 02 · HelloWorld与程序结构

## 今日目标
- 看懂一个 Go 源文件的完整结构
- 掌握 import、包名、main 函数的规则
- 学会用 gofmt 格式化、用 go doc 查文档

## 学习内容

### 一个 Go 文件的组成

把 `main.go` 拆开来逐行看：

```go
package main

import (
    "fmt"
    "math"
)

func main() {
    fmt.Println("Pi =", math.Pi)
}
```

| 部分 | 作用 |
|---|---|
| `package main` | 声明本文件属于哪个包。`main` 包表示这是一个可执行程序（而非库） |
| `import (...)` | 导入要用到的标准库或第三方包，多个包用括号分组 |
| `func main()` | 程序入口，**必须**在 main 包里、无参数无返回值 |
| 缩进与花括号 | Go 用 `gofmt` 统一格式，左花括号 `{` 不能另起一行 |

### import 的几种写法

```go
import "fmt"                    // 单行导入
import "math"

import (                        // 分组导入，推荐
    "fmt"
    "math"
)

import m "math"                 // 起别名，之后用 m.Pi
import . "fmt"                  // 点导入，之后直接用 Println（少用）
```

注意：**导入但未使用的包会编译报错**，变量同理——Go 对"没用上的东西"零容忍。

### 标识符可见性

Go 用**首字母大小写**决定对外是否可见：

- 首字母**大写**（如 `fmt.Println`、`math.Pi`）：导出，别的包可以访问。
- 首字母**小写**（如 `fmt.println` 不存在）：包内私有，外部访问不到。

这是 Go 没有 `public/private` 关键字的原因。

### 常用命令

```powershell
go run main.go      // 编译并运行
go build            // 编译出可执行文件
go fmt .            // 按官方风格格式化当前目录所有代码
go doc fmt.Println  // 查看标准库函数文档
go vet .            // 静态检查可疑代码
```

VS Code 装了 Go 扩展后会在保存时自动 `go fmt`，并在悬停时显示 `go doc` 文档。

## 核心重点
- 每个 Go 文件第一行必须是 `package 包名`；可执行程序必须是 `package main`
- 入口函数是 `func main()`，无参无返回值
- 导入的包、声明的变量都必须被使用，否则编译失败
- 首字母大写 = 导出（公开），小写 = 包内私有
- 保存即自动格式化，左花括号不能换行

## 练习/任务
1. 写一个程序，导入 `math` 包，打印 `math.Sqrt(144)` 的结果
2. 给 `math` 起别名为 `m`，用 `m.Pi` 打印圆周率
3. 故意导入一个包却不使用，观察编译报错信息
4. 运行 `go doc fmt.Printf` 查看文档，了解 Printf 用法

## 打卡清单
- [ ] 能说清 package、import、func main 各起什么作用
- [ ] 已体验"导入未使用包"的编译报错
- [ ] 已用 `go doc` 查过一个标准库函数
- [ ] 保存文件时代码被自动格式化
