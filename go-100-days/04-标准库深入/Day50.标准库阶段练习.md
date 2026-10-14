# Day 50 · 标准库阶段练习

## 今日目标
- 综合运用 strings/strconv/time/os/json/flag/log 写一个小工具
- 把标准库串成一个真实可用的命令行程序
- 完成阶段四算法练习

## 学习内容

### 项目：一个简易日志统计工具

需求：读一个文本文件，统计行数、单词数、出现频率最高的词，支持命令行参数。

```go
package main

import (
    "bufio"
    "flag"
    "fmt"
    "os"
    "sort"
    "strings"
)

func main() {
    path := flag.String("file", "", "要统计的文本文件")
    topN := flag.Int("top", 5, "显示频率前 N 的词")
    flag.Parse()

    f, err := os.Open(*path)
    if err != nil {
        fmt.Fprintln(os.Stderr, "打开失败:", err)
        os.Exit(1)
    }
    defer f.Close()

    lines := 0
    count := map[string]int{}
    sc := bufio.NewScanner(f)
    for sc.Scan() {
        lines++
        for _, w := range strings.Fields(sc.Text()) {
            w = strings.ToLower(strings.TrimFunc(w, func(r rune) bool {
                return !('a' <= r && r <= 'z')
            }))
            if w != "" {
                count[w]++
            }
        }
    }

    type kv struct {
        Word  string
        Count int
    }
    var sorted []kv
    for w, c := range count {
        sorted = append(sorted, kv{w, c})
    }
    sort.Slice(sorted, func(i, j int) bool {
        return sorted[i].Count > sorted[j].Count
    })

    fmt.Printf("文件 %s：共 %d 行\n", *path, lines)
    for i := 0; i < *topN && i < len(sorted); i++ {
        fmt.Printf("  %s: %d\n", sorted[i].Word, sorted[i].Count)
    }
}
```

要点：
- flag 解析参数
- os.Open + bufio.Scanner 逐行读
- strings.Fields 切词，ToLower 归一化
- map 计数，sort.Slice 排序

## 核心重点
- 真实工具就是标准库的组合
- flag 负责配置，os/file 负责 IO，strings 处理文本
- 错误要打印到 stderr 并 exit
- 排序用 sort.Slice 配 Less 函数

## 练习/任务
1. 跑通上面的统计工具，用一个文本文件测试
2. 加一个 -json 开关，结果以 JSON 输出
3. 加一个时间统计：用 time.Since 统计耗时
4. （选做）把日志输出到文件而不是 stdout

## 今日刷题

### 1. LeetCode 141 环形链表（复习）
- 思路：快慢指针。
- 代码提示：快追上慢即有环。
- Go 要点：指针操作。

### 2. LeetCode 142 环形链表 II
- 思路：快慢相遇后，一个指针从头、一个从相遇点同步走，再相遇即入口。
- 代码提示：数学推导结论。
- Go 要点：链表进阶。

## 打卡清单
- [ ] 跑通日志统计工具
- [ ] 加 JSON 输出
- [ ] 写完环形链表 II
- [ ] 阶段四所有标准库至少用过一次
