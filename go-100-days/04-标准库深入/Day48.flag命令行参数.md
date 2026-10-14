# Day 48 · flag命令行参数

## 今日目标
- 学会用 flag 包解析命令行参数
- 掌握字符串/整数/布尔参数
- 理解长短选项

## 学习内容

### 定义命令行参数

```go
import "flag"

name := flag.String("name", "Bob", "用户名")
age := flag.Int("age", 18, "年龄")
verbose := flag.Bool("v", false, "详细输出")

flag.Parse()

fmt.Println(*name, *age, *verbose)
```

运行：

```powershell
go run main.go -name Alice -age 30 -v
```

### flag 返回指针

注意 `flag.String` 返回的是 `*string`，要用 `*name` 取值。

### 位置参数

`flag.Parse()` 之后，`flag.Args()` 返回非选项参数：

```powershell
go run main.go -v file1.txt file2.txt
// flag.Args() = [file1.txt file2.txt]
```

### 布尔开关

`-v` 这种布尔开关不需要值，出现即 true。

### 帮助信息

不传参数或 `-h` 时，flag 自动打印用法和默认值。

### 典型 CLI 结构

```go
func main() {
    port := flag.Int("port", 8080, "监听端口")
    debug := flag.Bool("debug", false, "调试模式")
    flag.Parse()
    // ... 根据 *port *debug 启动服务
}
```

## 核心重点
- `flag.Type(名字, 默认值, 说明)` 定义参数，返回指针
- `flag.Parse()` 必须调用
- 用 `*name` 取值
- `flag.Args()` 取位置参数
- 布尔开关 `-v` 出现即 true

## 练习/任务
1. 写一个 CLI 工具，接收 -name 和 -age 并打印
2. 加一个 -v 开关，出现时打印详细日志
3. 接收多个位置参数作为文件名
4. 运行 `-h` 看自动生成的帮助信息

## 今日刷题

### 1. LeetCode 1 两数之和（复习）
- 思路：map 一次遍历。
- 代码提示：查 target - x。
- Go 要点：map 练习。

### 2. LeetCode 88 合并两个有序数组（复习）
- 思路：从后往前。
- 代码提示：三指针。
- Go 要点：双指针。

## 打卡清单
- [ ] 会定义 string/int/bool flag
- [ ] 会用 flag.Args 取位置参数
- [ ] 理解 flag 返回指针
- [ ] 复习两数之和
