# Day 13 · 基础综合练习

## 今日目标
- 把前 12 天的变量、类型、控制流、函数、指针、切片、map、字符串串起来
- 独立完成一个小命令行程序
- 排查第一次亲手写代码遇到的报错

## 学习内容

### 本期回顾

阶段一我们学了：

| 主题 | 关键能力 |
|---|---|
| 环境与结构 | go mod init、go run、package main、可见性 |
| 变量常量 | var/:=、零值、iota |
| 类型 | int/float64/string/bool、显式转换 |
| 控制流 | if、switch、for、range、break 标签 |
| 函数指针 | 多返回值、命名返回、值传递、指针改值 |
| 集合 | 切片 append/copy、map 增删查、字符串 rune |

### 示范：一个学生成绩统计小程序

下面这个例子综合用到了切片、map、函数、range、字符串处理：

```go
package main

import (
    "fmt"
    "strings"
)

// 求切片最大值
func maxScore(scores []int) int {
    m := scores[0]
    for _, s := range scores {
        if s > m {
            m = s
        }
    }
    return m
}

// 计算平均分，返回 avg 和是否及格
func passRate(scores []int) (float64, int) {
    sum := 0
    pass := 0
    for _, s := range scores {
        sum += s
        if s >= 60 {
            pass++
        }
    }
    return float64(sum) / float64(len(scores)), pass
}

func main() {
    class := map[string][]int{
        "一班": {80, 45, 90, 55},
        "二班": {70, 88, 62, 59, 100},
    }

    for class, scores := range class {
        avg, pass := passRate(scores)
        line := fmt.Sprintf("%s 共%d人 均分%.1f 最高%d 及格%d人",
            class, len(scores), avg, maxScore(scores), pass)
        fmt.Println(strings.Repeat("-", 2))
        fmt.Println(line)
    }
}
```

读懂这段代码比背概念重要：`map[string][]int`、多返回值、`for range`、`fmt.Sprintf` 都用上了。

## 核心重点
- 写真实小问题时，先想数据用什么结构（切片？map？结构体？）
- 函数按职责拆开，一个函数只做一件事
- 多返回值适合同时返回"结果 + 错误/统计"
- 遇到报错先读第一行错误信息，定位文件和行号

## 练习/任务
1. 把上面成绩程序敲一遍跑通，再加一个班"三班"
2. 写一个 `WordCount(s string) map[string]int`：统计字符串中每个单词出现次数（按空格切分）
3. 写一个 `Unique(nums []int) []int`：返回去重后的切片（提示：用 map 辅助）
4. （选做）写一个小程序，输入一段英文，打印其中出现次数最多的单词

## 打卡清单
- [ ] 独立跑通成绩统计示例
- [ ] 完成 WordCount 并能正确统计
- [ ] 完成 Unique 去重函数
- [ ] 阶段一所有语法点至少都亲手写过一次
