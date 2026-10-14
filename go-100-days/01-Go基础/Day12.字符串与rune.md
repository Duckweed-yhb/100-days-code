# Day 12 · 字符串与rune

## 今日目标
- 理解 Go 字符串是 UTF-8 字节序列
- 掌握 byte 与 rune 的区别
- 学会字符串遍历、拼接、常用转换

## 学习内容

### 字符串的本质

Go 字符串是**只读的字节切片**，内部按 UTF-8 编码存储 Unicode 字符。一个中文通常占 3 个字节。

```go
s := "你好Go"
fmt.Println(len(s))     // 8：你(3) 好(3) G(1) o(1)
```

注意 `len(s)` 返回**字节数**，不是字符数！

### byte 与 rune

- `byte` = `uint8`，表示一个字节。
- `rune` = `int32`，表示一个 Unicode 码点（一个字符）。

### 正确遍历中文

```go
s := "你好Go"
for i, r := range s {       // range 按 rune 解码
    fmt.Printf("下标%d 字符%c\n", i, r)
}
// 下标0 你
// 下标3 好
// 下标6 G
// 下标7 o
```

### 字符串与 []rune / []byte 互转

```go
s := "你好"
r := []rune(s)          // 转成 rune 切片，可按下标取字符
fmt.Println(string(r[0]))  // 你

b := []byte(s)          // 转字节切片
s2 := string(b)         // 再转回字符串
```

要按下标取"第 N 个字符"，必须先转 `[]rune`。

### 字符串拼接

```go
a := "Hello"
b := "Go"
c := a + " " + b                  // 简单拼接
// 大量拼接用 strings.Builder（Day39 详学）
```

### 常用操作（ strings 包预告）

```go
import "strings"
strings.Contains("hello", "ell")   // true
strings.ToUpper("hi")              // "HI"
strings.Split("a,b,c", ",")        // [a b c]
```

## 核心重点
- 字符串是只读 UTF-8 字节序列；`len()` 是字节数
- 遍历字符用 `for _, r := range s`，r 是 rune
- 要按字符下标访问，先 `[]rune(s)`
- `byte` 是字节，`rune` 是 Unicode 字符
- 字符串转 `[]byte`/`[]rune` 会拷贝，注意性能

## 练习/任务
1. 打印字符串 "Hello 世界" 的字节长度与字符长度
2. 用 range 遍历 "Go语言"，打印每个 rune 与其类型
3. 写一个函数 `Reverse(s string) string` 反转字符串（中文也正确）
4. 用 strings.Split 把 "a,b,c,d" 切成切片

## 打卡清单
- [ ] 理解字符串是 UTF-8 字节序列
- [ ] 会用 range 正确遍历中文
- [ ] 会在 string/[]rune/[]byte 之间转换
- [ ] 知道 len 是字节数不是字符数
