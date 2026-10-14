# Day 25 · 函数与接口阶段项目

## 今日目标
- 综合运用结构体、方法、接口、错误处理写一个小项目
- 体会面向接口设计的好处
- 完成阶段二第一次算法练习

## 学习内容

### 项目：一个简易形状面积计算器

需求：定义 `Shape` 接口，让 Circle、Rectangle、Triangle 实现 `Area() float64` 与 `Name() string`，并写一个函数批量打印面积。

```go
package main

import (
    "errors"
    "fmt"
    "math"
)

type Shape interface {
    Area() float64
    Name() string
}

type Circle struct{ Radius float64 }

func (c Circle) Area() float64 { return math.Pi * c.Radius * c.Radius }
func (c Circle) Name() string { return fmt.Sprintf("圆(r=%.2f)", c.Radius) }

type Rectangle struct{ W, H float64 }

func (r Rectangle) Area() float64 { return r.W * r.H }
func (r Rectangle) Name() string { return fmt.Sprintf("矩形(%.2fx%.2f)", r.W, r.H) }

func PrintAreas(shapes []Shape) {
    for _, s := range shapes {
        fmt.Printf("%s 面积 = %.2f\n", s.Name(), s.Area())
    }
}

func NewCircle(r float64) (Circle, error) {
    if r < 0 {
        return Circle{}, errors.New("半径不能为负")
    }
    return Circle{Radius: r}, nil
}

func main() {
    c, _ := NewCircle(2)
    shapes := []Shape{c, Rectangle{3, 4}}
    PrintAreas(shapes)
}
```

要点：
- `PrintAreas` 只依赖 `Shape` 接口，不关心具体类型——以后加 Triangle 不用改它。
- 构造函数 `NewCircle` 做参数校验并返回 error。
- 这就是"面向接口编程"。

## 核心重点
- 接口让函数参数与具体实现解耦
- 新增类型只要实现接口方法，老代码不用动
- 构造函数做校验，返回 (值, error)
- 方法集：值类型与指针类型的方法集不同，传值还是传指针要想清楚

## 练习/任务
1. 给项目加一个 `Triangle`（底、高）实现 Shape 接口
2. 给 `Rectangle` 加一个 `Perimeter()` 方法，再定义一个 `Polygons` 接口含 Perimeter
3. 让 `NewCircle(-1)` 返回错误并在 main 里打印
4. 把 `PrintAreas` 改成接收 `[]Shape` 并按面积排序输出

## 今日刷题

### 1. LeetCode 20 有效的括号
- 思路：用栈。遇到左括号入栈，遇到右括号弹出栈顶匹配；最后栈空即有效。
- 代码提示：`stack := []rune{}`；用 map 存右括号→对应左括号。
- Go 要点：切片当栈，`append` 压栈，`s[len(s)-1]` 取栈顶，`s[:len(s)-1]` 弹栈。

### 2. LeetCode 704 二分查找
- 思路：有序数组找目标值，双指针 `lo`、`hi`，每次取中点比较。
- 代码提示：`for lo <= hi { mid := lo + (hi-lo)/2; ... }`。
- Go 要点：整数除法 `(lo+hi)/2` 可能溢出，用 `lo+(hi-lo)/2` 更安全。

## 打卡清单
- [ ] 完成形状计算器并跑通
- [ ] 新增 Triangle 不修改 PrintAreas
- [ ] 写完 LeetCode 20 与 704
- [ ] 阶段二所有概念至少写过一次代码
