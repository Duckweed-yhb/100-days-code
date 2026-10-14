# Day 40 · 类型转换strconv

## 今日目标
- 掌握 string 与数值之间的转换
- 学会 Parse 与 Format 两类函数
- 理解 Atoi/Itoa 的区别

## 学习内容

### string 转数值

`strconv` 包负责字符串与基本类型转换：

```go
import "strconv"

// Atoi: string -> int
n, err := strconv.Atoi("42")        // n=42
// ParseInt 指定进制和位数
v, err := strconv.ParseInt("1010", 2, 64)  // 二进制 1010 = 10

// ParseFloat
f, err := strconv.ParseFloat("3.14", 64)

// ParseBool
b, err := strconv.ParseBool("true")
```

都返回 `(值, error)`，转换失败时 err 非 nil。

### 数值转 string

```go
s := strconv.Itoa(42)              // "42"
s2 := strconv.FormatInt(10, 2)    // "1010"，转二进制字符串
s3 := strconv.FormatFloat(3.14, 'f', 2, 64)  // "3.14"
```

### 易错点

```go
fmt.Sprintf("%d", 42)      // 也能转字符串，但比 Itoa 慢
```

简单数字转字符串优先用 `strconv.Itoa` / `FormatInt`，不要动不动就 Sprintf。

### Quote

```go
strconv.Quote("hi")        // "\"hi\""，带引号
strconv.Unquote(`"hi"`)    // "hi"
```

## 核心重点
- Atoi / Itoa 是 int 与 string 互转最常用
- ParseInt/ParseFloat/ParseBool 带错误返回
- FormatInt/FormatFloat 控制进制与精度
- 转换失败要检查 error
- 简单数字转串用 strconv，别滥用 Sprintf

## 练习/任务
1. 把字符串 "123" 转 int 再加 1 打印
2. 把 255 转成二进制字符串
3. 故意 Atoi("abc") 并处理 error
4. 写一个函数把 []string{"1","2","3"} 转成 []int

## 今日刷题

### 1. LeetCode 9 回文数（数学法，不用字符串）
- 思路：反转一半数字。
- 代码提示：负数 false；`rev := 0; for x > rev { rev = rev*10 + x%10; x/=10 }`。
- Go 要点：练习 strconv 与整数运算。

### 2. LeetCode 66 加一
- 思路：从末位往前处理进位。
- 代码提示：`for i := len(digits)-1; i>=0; i--`，逢 9 变 0 进位。
- Go 要点：理解数组进位。

## 打卡清单
- [ ] 会用 Atoi/Itoa
- [ ] 会用 ParseInt/FormatInt 指定进制
- [ ] 会处理转换错误
- [ ] 写完回文数与加一
