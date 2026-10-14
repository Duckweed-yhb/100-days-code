# Day 10 · 方法定义与参数

## 今日目标
- 学会定义与调用自己的方法
- 区分值参数、ref、out、params
- 理解方法为什么能让代码更清晰

## 学习内容

### 为什么需要方法

方法就是"一段起了名字、可重复调用的代码"。把重复逻辑抽出去，主程序才不会变成一大坨面条。

### 定义与调用

```csharp
// 修饰符  返回类型  方法名(参数列表)
static int Add(int a, int b)
{
    return a + b;
}

// 调用
int sum = Add(3, 5);
Console.WriteLine(sum);     // 8
```

顶级语句里，方法写在文件底部即可（C# 6+ 允许）：

```csharp
Console.WriteLine(IsEven(4) ? "偶数" : "奇数");

static bool IsEven(int n) => n % 2 == 0;   // 表达式体方法
```

### 参数传递：默认按值传递

```csharp
static void TryChange(int x)
{
    x = 999;        // 只改了副本
}

int a = 1;
TryChange(a);
Console.WriteLine(a);   // 还是 1
```

默认传的是"副本"，方法里改的是副本，外面不变。

### ref：按引用传递

要让方法真正改外面的变量，加 `ref`：

```csharp
static void Swap(ref int x, ref int y)
{
    int t = x; x = y; y = t;
}

int p = 1, q = 2;
Swap(ref p, ref q);
Console.WriteLine($"{p} {q}");   // 2 1
```

### out：方法往外传结果

`out` 用来让方法"顺便"多返回一个值（前面 `int.TryParse` 就用过）：

```csharp
static void DivMod(int a, int b, out int quotient, out int remainder)
{
    quotient = a / b;
    remainder = a % b;
}

DivMod(17, 5, out int q, out int r);
Console.WriteLine($"{q} 余 {r}");   // 3 余 2
```

### params：可变参数个数

```csharp
static int SumAll(params int[] nums)
{
    int sum = 0;
    foreach (int n in nums) sum += n;
    return sum;
}

Console.WriteLine(SumAll(1, 2, 3));          // 6
Console.WriteLine(SumAll(1, 2, 3, 4, 5));    // 15
```

### 可选参数与具名参数

```csharp
static void Greet(string name, string greeting = "你好")
{
    Console.WriteLine($"{greeting}，{name}");
}

Greet("余泓彬");                       // 你好，余泓彬
Greet("余泓彬", greeting: "晚上好");    // 用参数名指定
```

## 核心重点
- 方法 = 返回类型 + 名字 + 参数列表 + 方法体
- 默认按值传递，方法内改副本不影响外面
- `ref` 改外面变量；`out` 让方法多出参；`params` 接可变个数
- 可选参数给默认值，调用时用 `参数名: 值` 具名传参
- 一个方法只做一件事，名字要见名知意

## 练习/任务
1. 写方法 `IsPrime(int n)` 判断质数，返回 bool，主程序打印 2~100 的所有质数
2. 写方法 `Max(params int[] nums)` 返回最大值，用若干个数测试
3. 用 `out` 写方法把一个 int 拆成"是否正数、绝对值、符号"三个结果
4. （选做）写方法 `PrintTriangle(int n)` 打印 n 行星号三角形

## 打卡清单
- [ ] 会定义有返回值的方法并调用
- [ ] 理解按值传递不会改外面变量
- [ ] 会用 out 让方法多返回值
- [ ] 会用 params 写可变参数方法
