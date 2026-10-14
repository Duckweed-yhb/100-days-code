# Day 04 · 基本数据类型

## 今日目标
- 掌握 C# 常用的值类型：整数、浮点、布尔、字符
- 理解 `decimal` 为什么适合钱
- 知道值类型与引用类型的初步区别（明天展开）

## 学习内容

### 整数类型

| 类型 | 字节数 | 范围 | 用法 |
|---|---|---|---|
| `int` | 4 | ±21 亿 | 默认整数 |
| `long` | 8 | ±9×10¹⁸ | 大整数、时间戳 |
| `short` | 2 | ±3 万 | 少见，省内存时用 |
| `byte` | 1 | 0~255 | 二进制数据 |

整数字面量默认是 `int`；要写 `long` 加后缀 `L`：

```csharp
int a = 1000000;
long big = 9000000000L;
```

### 浮点类型

| 类型 | 特点 | 适用 |
|---|---|---|
| `float` | 单精度，后缀 `f` | 图形、游戏，省内存 |
| `double` | 双精度，默认浮点 | 科学计算 |
| `decimal` | 高精度十进制 | **钱、金额** |

```csharp
double x = 0.1;
double y = 0.2;
Console.WriteLine(x + y);   // 可能输出 0.30000000000000004

decimal price = 19.99m;      // 金额用 decimal，后缀 m
```

> 为什么钱不能用 double？二进制浮点无法精确表示 0.1，多次累加会出错。金额一律用 `decimal`。

### 布尔与字符

```csharp
bool isPass = true;          // 只有 true / false
char grade = 'A';            // 单字符，用单引号
char digit = '7';
```

C# 的 `bool` 就是 `true/false`，不能像 C 那样拿 0/1 当布尔用。

### 字符串

`string` 是引用类型（今天先认识）：

```csharp
string s1 = "hello";
string s2 = "你好";
string empty = "";           // 空串
```

### 类型默认值与字面量后缀速查

| 写法 | 类型 |
|---|---|
| `123` | int |
| `123L` | long |
| `1.23` | double |
| `1.23f` | float |
| `1.23m` | decimal |
| `true` / `false` | bool |
| `'a'` | char |
| `"a"` | string |

## 核心重点
- 整数默认 int，大数用 long 加 L；浮点默认 double
- 钱必须用 decimal（后缀 m），不要用 double
- bool 只有 true/false；char 用单引号存单个字符
- string 用双引号，是引用类型
- 选类型原则：范围够小就别用大类型，但别为省内存选到溢出

## 练习/任务
1. 声明 double 与 decimal 各存 0.1，各加 3 次，对比输出差异
2. 用 decimal 计算两件商品 19.99 与 25.5 打八折后的总价
3. 声明 char 变量存你的姓的首字母，输出它和它的数字编码（提示：强制转 int）
4. （选做）查 int.MaxValue，故意让两个 int 相加溢出，看结果

## 打卡清单
- [ ] 会选 int/long/byte 存不同范围整数
- [ ] 已用 decimal 算金额并理解为何不用 double
- [ ] 能区分 char 与 string 的引号用法
- [ ] 知道常见字面量后缀 L/f/m
