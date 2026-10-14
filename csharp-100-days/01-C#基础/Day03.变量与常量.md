# Day 03 · 变量与常量

## 今日目标
- 掌握变量声明、赋值与命名规则
- 区分 `var` 类型推断与显式类型
- 学会用 `const` 定义常量，理解为什么要常量

## 学习内容

### 变量是什么

变量是一块有名字的内存，用来存数据。C# 是**静态类型**语言：声明变量时就要说清它存什么类型，类型定了之后不能随便改存别的类型。

```csharp
int age;          // 声明一个 int 类型变量，名字叫 age
age = 18;         // 赋值
int score = 95;   // 声明同时赋值
```

### 命名规则

| 规则 | 正确示例 | 错误示例 |
|---|---|---|
| 字母、下划线、@ 开头 | `age`、`_name`、`@class` | `1age` |
| 不能是关键字 | `studentAge` | `int`（除非加 @） |
| 区分大小写 | `Age` 与 `age` 是两个变量 | — |

约定俗成：局部变量用 camelCase（`studentAge`），方法名与类用 PascalCase（`CalculateTotal`）。

### var 类型推断

编译器能从右边的值推断出类型时，可以用 `var`：

```csharp
var name = "余泓彬";     // 推断为 string
var count = 10;          // 推断为 int
var price = 12.5;        // 推断为 double
```

> 注意：`var` 不是"任意类型"，一旦推断出来就固定了，下面再赋值成别的类型会报错。

### 常量 const

不会变的值用 `const` 声明，编译期就确定：

```csharp
const double Pi = 3.14159;
const string Version = "1.0.0";
// Pi = 3.14;   // 错误：常量不能再赋值
```

常量的好处：防止误改、给魔法数字起名字、编译器可优化。

### 程序里的小例子

```csharp
string studentName = "余泓彬";
int chinese = 90, math = 95, english = 88;   // 一行声明多个同类型变量
int total = chinese + math + english;
double average = total / 3.0;

Console.WriteLine($"{studentName} 总分 {total}，平均分 {average:F2}");
```

`{average:F2}` 表示保留两位小数，这是格式化字符串的写法。

## 核心重点
- 静态类型：变量声明时定类型，之后不能换类型
- 命名用 camelCase 表示局部变量，避开关键字
- `var` 让编译器推断类型，但类型一旦确定不可变
- `const` 定义编译期常量，不能再赋值
- 格式化字符串 `{表达式:F2}` 控制小数位数

## 练习/任务
1. 声明 3 个 int 变量存三门课成绩，输出总分与平均分（保留一位小数）
2. 用 `const` 定义一周天数 7，写程序把"X 天是几周零几天"算出来
3. 故意写一行 `var x = 5; x = "abc";` 看编译报什么错，理解静态类型
4. （选做）查资料说出 `readonly` 与 `const` 的区别（提示：一个编译期，一个运行期）

## 打卡清单
- [ ] 能正确声明与赋值不同类型的变量
- [ ] 已使用 var 并理解它仍是静态类型
- [ ] 已用 const 定义常量并避免魔法数字
- [ ] 会用 :F2 之类的格式化输出数字
