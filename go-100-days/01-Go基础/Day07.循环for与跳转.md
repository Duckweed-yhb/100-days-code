# Day 07 · 循环for与跳转

## 今日目标
- 掌握 Go 中唯一的循环关键字 for 的三种形态
- 学会 range 遍历切片/map/字符串
- 理解 break / continue / goto

## 学习内容

### Go 只有 for，没有 while / do-while

```go
// 标准三段式
for i := 0; i < 5; i++ {
    fmt.Println(i)
}

// 当 while 用
n := 10
for n > 0 {
    n--
}

// 无限循环
for {
    // break 退出
}
```

### range 遍历

```go
nums := []int{10, 20, 30}
for i, v := range nums {     // i 是下标，v 是副本
    fmt.Println(i, v)
}

for k, v := range map[string]int{"a": 1, "b": 2} {
    fmt.Println(k, v)
}

for _, v := range nums {     // 只要值不要下标
    fmt.Println(v)
}
```

注意：`v` 是每个元素的**拷贝**，修改 `v` 不会改原切片。

### break 与 continue

```go
for i := 0; i < 10; i++ {
    if i == 3 {
        continue      // 跳过本次
    }
    if i == 7 {
        break         // 跳出循环
    }
    fmt.Println(i)
}
```

### 带标签的 break

跳出多层嵌套循环：

```go
outer:
for i := 0; i < 3; i++ {
    for j := 0; j < 3; j++ {
        if i+j == 3 {
            break outer       // 直接跳出外层
        }
    }
}
```

### goto（少用）

Go 保留 `goto`，但只允许在函数内向前跳，规范里几乎不用，知道即可。

## 核心重点
- Go 只有 `for`，把 while、无限循环都吸收了
- `for i, v := range 集合` 中下标与值都给，`v` 是副本
- `break` 跳出当前循环，`continue` 跳过本次
- 多层跳出用 `break 标签`
- 没有 while / do-while，别去找

## 练习/任务
1. 用 for 计算 1 到 100 的和
2. 用 range 遍历一个字符串，逐个打印每个 rune
3. 写两层循环找第一个 `i*j == 12` 的组合，用带标签 break 退出
4. 用 continue 打印 1~20 中所有偶数

## 打卡清单
- [ ] 会写三段式 for、while 式 for、无限 for
- [ ] 会用 range 遍历切片和 map
- [ ] 知道 range 的值是副本
- [ ] 会用带标签 break 跳出多层
