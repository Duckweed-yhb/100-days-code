# Day 04 · 基本数据类型

## 今日目标
- 掌握 Go 的整数、浮点、布尔、字符串四大类类型
- 理解有符号/无符号、按位长度的区别
- 知道类型别名与自定义类型

## 学习内容

### 整数类型

| 类别 | 类型 | 说明 |
|---|---|---|
| 有符号 | int8 int16 int32 int64 | 按位宽指定范围 |
| 无符号 | uint8 uint16 uint32 uint64 | 非负 |
| 平台相关 | int / uint | 32 位系统 32 位、64 位系统 64 位 |
| 特殊 | byte (=uint8)、rune (=int32)、uintptr | byte 表示字节，rune 表示 Unicode 码点 |

日常业务**一律用 `int`** 即可，只有性能敏感或二进制协议才需要指定位宽。

### 浮点与复数

```go
var f1 float32 = 3.14
var f2 float64 = 2.71828          // 推荐默认用 float64
x := 1.5                          // 不带类型的浮点字面量默认是 float64
```

### 布尔与字符串

```go
var ok bool = true
s := "Go 语言"          // 字符串是 UTF-8 字节序列，不可变
```

字符串底层是只读字节数组，用反引号 `` ` `` 写多行原始字符串：

```go
sql := `SELECT * FROM users
WHERE id = 1;`
```

### 类型转换

Go **没有自动隐式类型转换**，必须显式写：

```go
var i int = 10
var f float64 = float64(i)   // 必须显式转换
var u uint = uint(f)
```

### 自定义类型与别名

```go
type UserID int              // 定义新类型，与 int 不互通
type MyString = string       // 类型别名，完全等价于 string
```

## 核心重点
- 整数默认 `int`，字节用 `byte`，Unicode 码点用 `rune`
- 浮点默认用 `float64`
- 字符串是 UTF-8、不可变；反引号写原始多行串
- Go 不做隐式类型转换，必须 `T(x)` 显式转换
- `type 新名 底层类型` 定义新类型；`type 别名 = 类型` 是别名

## 练习/任务
1. 声明 int、float64、bool、string 各一个并打印
2. 把一个 int 变量转成 float64，再转成 string 之前先想想为什么不能直接转
3. 用反引号写一段多行 SQL 字符串并打印
4. 定义 `type Celsius float64`，声明一个该类型变量

## 打卡清单
- [ ] 记住 int/byte/rune/float64/bool/string 的用途
- [ ] 理解 Go 无隐式类型转换
- [ ] 会用反引号写多行字符串
- [ ] 分得清新类型与类型别名
