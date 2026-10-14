# Day 44 · 字节缓冲与字节序

## 今日目标
- 掌握 bytes.Buffer 的用法
- 理解二进制数据读写 encoding/binary
- 知道大端小端区别

## 学习内容

### bytes.Buffer

bytes.Buffer 是内存中的字节缓冲区，同时实现 Reader 和 Writer：

```go
var buf bytes.Buffer
buf.WriteString("hello")
buf.WriteByte(' ')
buf.Write([]byte("world"))
fmt.Println(buf.String())     // "hello world"
```

常用于拼字节流、测试 mock。

### 与 strings.Builder 对比

| 类型 | 用途 |
|---|---|
| strings.Builder | 只写字符串，最高效 |
| bytes.Buffer | 读写都行，方法更多 |

### 字节序 encoding/binary

网络协议、二进制文件要按固定字节序读写多字节整数：

```go
import "encoding/binary"

var buf bytes.Buffer
binary.Write(&buf, binary.BigEndian, int32(1))   // 大端写入
binary.Write(&buf, binary.LittleEndian, int32(1)) // 小端写入
```

- **大端（BigEndian）**：高位字节在前，网络字节序默认大端。
- **小端（LittleEndian）**：低位字节在前，x86 CPU 默认。

### Read 端

```go
var n int32
binary.Read(&buf, binary.BigEndian, &n)
```

读写必须字节序一致，否则解析出乱码。

## 核心重点
- bytes.Buffer 内存缓冲，同时读写
- 纯拼字符串用 strings.Builder 更轻
- encoding/binary 按固定字节序读写整数
- 网络协议用大端
- 读写字节序必须一致

## 练习/任务
1. 用 bytes.Buffer 拼接多种类型后输出字符串
2. 用 binary.Write 写一个 int32 大端，再 binary.Read 读回
3. 对比 strings.Builder 与 bytes.Buffer 的拼接性能
4. 写一个简单的"长度+字符串"二进制编码

## 今日刷题

### 1. LeetCode 88 合并两个有序数组（复习）
- 思路：从后往前双指针。
- 代码提示：`i, j, k := m-1, n-1, m+n-1`。
- Go 要点：原地合并。

### 2. LeetCode 66 加一（复习）
- 思路：末位进位。
- 代码提示：逢 9 归零进位。
- Go 要点：边界最高位进位要扩容。

## 打卡清单
- [ ] 会用 bytes.Buffer
- [ ] 会用 encoding/binary 读写整数
- [ ] 理解大端小端
- [ ] 复习两道数组题
