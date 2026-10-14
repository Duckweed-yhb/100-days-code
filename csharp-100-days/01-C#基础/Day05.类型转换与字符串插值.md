# Day 05 · 类型转换与字符串插值

## 今日目标
- 掌握隐式转换与显式转换（强制类型转换）
- 学会用 `Convert` 与 `int.Parse` / `int.TryParse` 把字符串转数字
- 熟练使用字符串插值与常用格式化

## 学习内容

### 隐式转换（小范围 → 大范围）

不会丢精度的转换编译器自动做：

```csharp
int i = 100;
long l = i;        // int 自动转 long，没问题
double d = i;      // int 自动转 double
```

反过来（大范围 → 小范围）会丢精度，必须**显式转换**：

```csharp
double pi = 3.14;
int n = (int)pi;        // 强制转，结果 3，小数被截断
long big = 300;
byte b = (byte)big;     // 超出 byte 范围会溢出，不报错
```

### 字符串与数字互转

`Console.ReadLine()` 拿到的是字符串，要算就得转：

```csharp
Console.Write("输入年龄：");
string input = Console.ReadLine()!;
int age = int.Parse(input);          // 转成功得 int，失败抛异常
Console.WriteLine($"明年 {age + 1}");
```

更安全的写法——`TryParse`，失败不抛异常：

```csharp
Console.Write("输入一个数：");
string s = Console.ReadLine()!;
if (int.TryParse(s, out int num))
{
    Console.WriteLine($"你输入的是 {num}，平方 {num * num}");
}
else
{
    Console.WriteLine("这不是一个整数！");
}
```

反向：数字转字符串用 `.ToString()`：

```csharp
int x = 42;
string sx = x.ToString();
```

### 字符串插值与格式化

```csharp
string name = "余泓彬";
int score = 95;
Console.WriteLine($"姓名：{name}，成绩：{score}");

decimal price = 1234.5678m;
Console.WriteLine($"价格：{price:F2}");     // 货币两位小数
Console.WriteLine($"价格：{price:C}");      // 货币格式，带￥
double ratio = 0.8521;
Console.WriteLine($"占比：{ratio:P1}");    // 百分比 85.2%
```

| 格式符 | 含义 | 示例输出 |
|---|---|---|
| `F2` | 定点两位小数 | 1234.57 |
| `C` | 货币 | ￥1,234.57 |
| `P1` | 百分比一位 | 85.2% |
| `D5` | 补零到 5 位 | 00042 |

### 常见坑

- `int a = "123";` 直接赋值报错，必须显式转；
- `int.Parse("12.5")` 会抛异常，小数用 `double.Parse`；
- 除法 `5 / 2` 得 2（整数除法），要得 2.5 写 `5.0 / 2`。

## 核心重点
- 小转大隐式，大转小要 `(类型)` 强转，小数会被截断
- 字符串转数字用 `int.Parse`，用户输入用 `int.TryParse` 更安全
- `out` 关键字把转换结果带出来
- `$"..."` 插值，`:F2` `:C` `:P1` 控制格式
- 整数除法丢小数，想留小数就用浮点字面量

## 练习/任务
1. 读用户输入的两个整数，输出它们的和、差、商（保留两位小数）
2. 用 `TryParse` 改上面程序，输入非法时提示"请输入数字"而不是崩溃
3. 把 decimal 金额用 `:C` 格式输出，观察人民币符号
4. （选做）用 `:D5` 把 7 格式化成 00007，想想什么时候用得到（学号、订单号）

## 打卡清单
- [ ] 会写强制类型转换并知道会截断小数
- [ ] 已用 int.TryParse 安全解析用户输入
- [ ] 熟练使用 $ 插值与 F2/C/P1 格式化
- [ ] 已踩过整数除法的坑并理解原因
