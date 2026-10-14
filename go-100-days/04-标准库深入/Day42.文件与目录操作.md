# Day 42 · 文件与目录操作

## 今日目标
- 学会读写文本文件
- 掌握 defer Close 的资源管理
- 了解目录创建与遍历

## 学习内容

### 写文件

```go
content := []byte("hello file\n第二行")
err := os.WriteFile("test.txt", content, 0644)
if err != nil {
    log.Fatal(err)
}
```

`os.WriteFile` 一步写小文件最方便。

### 读文件

```go
data, err := os.ReadFile("test.txt")
if err != nil {
    log.Fatal(err)
}
fmt.Println(string(data))
```

### 大文件流式读写

大文件别一次性读入内存，用 os.Open + bufio：

```go
f, err := os.Open("test.txt")
if err != nil {
    log.Fatal(err)
}
defer f.Close()            // 记得关闭

scanner := bufio.NewScanner(f)
for scanner.Scan() {
    line := scanner.Text()
    fmt.Println(line)
}
```

### 创建目录

```go
os.Mkdir("mydir", 0755)                 // 单层
os.MkdirAll("a/b/c", 0755)             // 递归创建
```

### 判断文件是否存在

```go
_, err := os.Stat("test.txt")
if os.IsNotExist(err) {
    fmt.Println("不存在")
}
```

### 权限位

`0644` = 文件所有者读写、其他只读；`0755` = 目录/可执行文件。

## 核心重点
- 小文件用 `os.ReadFile` / `os.WriteFile` 一步到位
- 大文件用 `os.Open` + `bufio.Scanner` 逐行读
- `defer f.Close()` 防泄漏
- `MkdirAll` 递归建目录
- `os.IsNotExist` 判断存在性

## 练习/任务
1. 写一个 "hello\nworld" 到 file.txt 再读出来打印
2. 用 bufio.Scanner 逐行读取一个文件
3. 用 MkdirAll 创建 nested/dir/structure
4. 判断一个不存在的文件，打印"文件不存在"

## 今日刷题

### 1. LeetCode 217 存在重复元素
- 思路：map 去重，遍历遇到已存在的 key 返回 true。
- 代码提示：`seen := map[int]bool{}`。
- Go 要点：set 用 map[T]bool 模拟。

### 2. LeetCode 136 只出现一次的数字
- 思路：异或，x^x=0、x^0=x，全部异或结果就是那个数。
- 代码提示：`r := 0; for _, n := range nums { r ^= n }`。
- Go 要点：位运算技巧。

## 打卡清单
- [ ] 会读写小文件
- [ ] 会流式读大文件
- [ ] 会建目录、判断存在性
- [ ] 写完重复元素与单次数字题
