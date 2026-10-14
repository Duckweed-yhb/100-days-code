# Day 12 · 字符串操作

## 今日目标
- 掌握字符串的常用方法：查找、截取、替换、分割
- 会用 `String.Split` 与 `string.Join`
- 理解字符串不可变性

## 学习内容

### 字符串常用方法

```csharp
string s = "  Hello, CSharp!  ";

Console.WriteLine(s.Length);          // 长度（含空格）
Console.WriteLine(s.Trim());         // 去掉首尾空白
Console.WriteLine(s.ToUpper());       // 转大写
Console.WriteLine(s.ToLower());       // 转小写
Console.WriteLine(s.Contains("CSharp"));   // true，是否包含子串
Console.WriteLine(s.StartsWith("  Hello")); // true
Console.WriteLine(s.IndexOf("C"));   // 子串首次出现的位置，找不到返回 -1
```

### 截取与替换

```csharp
string name = "余泓彬";
string sub = name.Substring(1, 2);   // 从下标 1 开始取 2 个字符："泓彬"

string phone = "138-1234-5678";
string noDash = phone.Replace("-", "");   // 13812345678
```

### 分割与拼接

```csharp
string csv = "苹果,香蕉,橙子,葡萄";
string[] fruits = csv.Split(',');
foreach (string f in fruits) Console.WriteLine(f);

string joined = string.Join(" | ", fruits);
Console.WriteLine(joined);   // 苹果 | 香蕉 | 橙子 | 葡萄
```

### 字符串不可变性

```csharp
string a = "hello";
string b = a.ToUpper();
Console.WriteLine(a);   // hello 没变
Console.WriteLine(b);   // HELLO
```

> 字符串一旦创建就不能改。`ToUpper`、`Replace` 都返回**新字符串**，原字符串不动。频繁拼接字符串会产生大量临时对象，性能差——后面讲 StringBuilder。

### 判空与安全

```csharp
string? input = Console.ReadLine();

if (string.IsNullOrEmpty(input))           // 为 null 或空串
    Console.WriteLine("你没输入");
else if (string.IsNullOrWhiteSpace(input)) // null 或全是空白
    Console.WriteLine("输入全是空格");
else
    Console.WriteLine($"你输入了 {input.Length} 个字符");
```

### 小例子：统计单词数

```csharp
string text = "the quick brown fox jumps over the lazy dog";
string[] words = text.Split(' ', StringSplitOptions.RemoveEmptyEntries);
Console.WriteLine($"共 {words.Length} 个单词");
foreach (var w in words) Console.WriteLine(w);
```

## 核心重点
- `Trim/ToUpper/ToLower/Contains/IndexOf` 是高频 API
- `Substring(起始, 长度)` 截取；`Replace(旧, 新)` 替换
- `Split` 拆成数组，`string.Join` 拼回字符串
- 字符串不可变：所有"修改"都返回新串
- 判空用 `IsNullOrEmpty` / `IsNullOrWhiteSpace`

## 练习/任务
1. 输入一句英文，统计其中单词个数与最长单词
2. 把一个手机号 `13812345678` 格式化成 `138-1234-5678`
3. 判断用户输入的字符串是否是回文（正读反读一样，如 abcba）
4. （选做）统计一句话中每个字母出现次数（不区分大小写）

## 打卡清单
- [ ] 会用 Split 与 Join 拆拼字符串
- [ ] 会用 Substring 截取与 Replace 替换
- [ ] 理解字符串不可变
- [ ] 会用 IsNullOrWhiteSpace 做输入校验
