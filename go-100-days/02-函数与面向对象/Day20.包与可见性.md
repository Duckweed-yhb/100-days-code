# Day 20 · 包与可见性

## 今日目标
- 理解 Go 的包组织方式
- 掌握首字母大小写即导出
- 学会在模块内组织多文件包

## 学习内容

### 包是什么

一个目录下的所有 `.go` 文件必须同属一个包，第一行 `package xxx`。包是 Go 复用代码的基本单位。

```
mymath/
  go.mod
  math/
    add.go       package math
    sub.go       package math
  main.go        package main
```

### 导出规则

| 写法 | 可见性 |
|---|---|
| `Add(x, y int)` | 大写开头，外部可访问 |
| `add(x, y int)` | 小写开头，包内私有 |
| `type User struct` | 导出类型 |
| `type user struct` | 包内私有类型 |

连结构体字段也一样：导出字段才能被别的包读写、才能被 JSON 序列化。

### 导入自己的包

`go.mod` 里模块名假设是 `example.com/demo`：

```go
// main.go
package main

import "example.com/demo/mymath"   // 导入子包

func main() {
    fmt.Println(mymath.Add(1, 2))
}
```

### init 函数

每个包可以有多个 `init()`，在包被导入时自动执行（在 main 之前），常用于注册驱动、初始化常量。日常业务少写 init，避免隐藏依赖。

### 包惯例

- 包名简短、小写、单数：`http`、`time`、`user`
- 不用 `util`、`common` 这种大杂烩包名
- 一个包尽量一件事

## 核心重点
- 一个目录一个包，所有文件同包名
- 首字母大写导出，小写私有
- 导入路径是 `模块名/子目录`
- `init()` 自动执行，少用
- 包名要短而专

## 练习/任务
1. 建一个 `mymath` 包，导出 `Add` 和私有 `validate`
2. 在 main 里导入并调用 `mymath.Add`，尝试访问私有函数看报错
3. 给结构体加导出字段与未导出字段，观察外部能否访问
4. 写一个 init() 打印初始化信息，观察执行时机

## 打卡清单
- [ ] 理解包目录结构
- [ ] 会用首字母大小写控制可见性
- [ ] 能在模块内导入自己写的包
- [ ] 知道 init 的执行时机
