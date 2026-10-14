# Day 43 · io输入输出接口

## 今日目标
- 理解 io.Reader / io.Writer 是 Go IO 抽象核心
- 学会用 io.Copy、io.Pipe
- 掌握 stdin/stdout 操作

## 学习内容

### io.Reader 与 io.Writer

Go 把所有输入输出抽象成两个接口：

```go
type Reader interface {
    Read(p []byte) (n int, err error)
}
type Writer interface {
    Write(p []byte) (n int, err error)
}
```

文件、网络连接、内存 buffer、HTTP 响应体都实现了它们——这就是为什么 `io.Copy` 能在任意 Reader/Writer 之间搬数据。

### io.Copy 搬数据

```go
// 把 src 内容复制到 dst，不用关心具体类型
written, err := io.Copy(dst, src)
```

下载文件、复制响应体常用：

```go
resp, _ := http.Get("https://example.com")
defer resp.Body.Close()
out, _ := os.Create("page.html")
defer out.Close()
io.Copy(out, resp.Body)
```

### io 包常用函数

```go
io.Copy(dst, src)            // 复制
io.CopyN(dst, src, 1024)     // 复制指定字节
io.ReadAll(r)                // 读光到 []byte（Go1.16+）
```

### 标准输入输出

```go
fmt.Fprintln(os.Stdout, "标准输出")
io.Copy(os.Stdout, os.Stdin)   // 把 stdin 原样输出
```

### strings.Reader / bytes.Buffer

```go
r := strings.NewReader("hello")   // 字符串当 Reader
var buf bytes.Buffer
buf.WriteString("hi")             // 内存当 Writer
```

## 核心重点
- io.Reader / io.Writer 是 IO 抽象的灵魂
- 任意 Reader 都能 Copy 到任意 Writer
- 文件、网络、HTTP Body 都实现这俩接口
- io.ReadAll 读光 Reader
- 用接口抽象，不要写死具体类型

## 练习/任务
1. 用 io.Copy 把一个文件复制到另一个文件
2. 用 strings.NewReader 包一段字符串当 Reader 读
3. 用 bytes.Buffer 在内存里写一段再读出来
4. 写一个程序把 stdin 原样写到 stdout

## 今日刷题

### 1. LeetCode 35 搜索插入位置
- 思路：二分，找第一个 >= target 的位置。
- 代码提示：标准二分变体。
- Go 要点：二分模板练习。

### 2. LeetCode 69 x 的平方根
- 思路：二分 0~x，找最大的 n 使 n*n <= x。
- 代码提示：注意溢出，用除法比较。
- Go 要点：整数二分。

## 打卡清单
- [ ] 理解 Reader/Writer 抽象
- [ ] 会用 io.Copy 搬数据
- [ ] 会用 strings.Reader / bytes.Buffer
- [ ] 写完二分插入与开方题
