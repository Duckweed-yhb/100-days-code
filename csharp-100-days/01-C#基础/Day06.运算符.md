# Day 06 · 运算符

## 今日目标
- 掌握算术、赋值、比较、逻辑四类运算符
- 理解自增自减 `++` 前缀与后缀的区别
- 了解位运算与短路求值

## 学习内容

### 算术运算符

```csharp
int a = 10, b = 3;
Console.WriteLine(a + b);    // 13
Console.WriteLine(a - b);    // 7
Console.WriteLine(a * b);    // 30
Console.WriteLine(a / b);    // 3（整数除法）
Console.WriteLine(a % b);    // 1（取余）
```

### 赋值与复合赋值

```csharp
int x = 10;
x += 5;     // 等价 x = x + 5，得 15
x -= 3;     // 12
x *= 2;     // 24
x /= 4;     // 6
```

### 自增自减

```csharp
int i = 5;
int a = i++;     // 后缀：先把 i 原值给 a，再 i+1。a=5, i=6
int j = 5;
int c = ++j;     // 前缀：先 j+1，再给 c。c=6, j=6
```

> 记忆：**前缀先加后用，后缀先用后加**。实际项目里建议单独一行写 `i++`，别混在表达式里。

### 比较运算符

```csharp
10 > 3       // true
10 >= 10     // true
5 == 5       // true，注意是两个等号
5 != 3       // true
```

结果都是 `bool`。**判断相等用 `==`，一个 `=` 是赋值**，新手最容易写错。

### 逻辑运算符

```csharp
bool ageOk = age >= 18;
bool idOk = hasIdCard;
bool canEnter = ageOk && idOk;    // 与：两个都真才真
bool vip = isVip || hasCoupon;    // 或：一个真就真
bool notBlocked = !isBlocked;     // 非：取反
```

`&&` 和 `||` 是**短路**：`a && b` 如果 a 已经 false，b 直接不算了；`a || b` 如果 a 已经 true，b 也不算。

### 字符串拼接也用 +

```csharp
string s = "abc" + 123;     // "abc123"
```

## 核心重点
- `/` 对整数做整除，`%` 取余判奇偶很常用
- `++i` 与 `i++` 区别：先加 vs 后加
- 相等判断是 `==`，赋值是 `=`，别混
- `&&` `||` 短路求值；`!` 取反
- 运算符优先级记不住就加括号，括号永远清晰

## 练习/任务
1. 读一个三位数整数，分别输出它的百位、十位、个位（提示：用 / 与 %）
2. 写程序判断用户输入的年份是不是闰年（能被 4 整除但不能被 100 整除，或能被 400 整除）
3. 写一段代码观察 `int i=5; Console.WriteLine(i++); Console.WriteLine(++i);` 的输出
4. （选做）用短路逻辑写：只有 age>0 时才判断 age<150，避免 age 为负时报错

## 打卡清单
- [ ] 会用 / 与 % 拆数字各位
- [ ] 分清 == 与 =
- [ ] 理解前缀后缀 ++ 的差异
- [ ] 知道 && 是短路与
