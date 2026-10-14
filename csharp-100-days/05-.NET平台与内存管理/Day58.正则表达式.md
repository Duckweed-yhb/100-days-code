# Day 58 · 正则表达式

## 今日目标
- 会用 `Regex` 做匹配、提取、替换
- 掌握常用正则元字符
- 能用正则解决表单验证问题

## 学习内容

### 正则是什么

正则表达式是一套描述字符串模式的迷你语言。C# 里用 `System.Text.RegularExpressions.Regex`：

```csharp
using System.Text.RegularExpressions;

bool isEmail = Regex.IsMatch("test@example.com", @"^[\w.]+@[\w.]+\.\w+$");
Console.WriteLine(isEmail);   // True
```

### 常用元字符

| 符号 | 含义 |
|---|---|
| `.` | 任意字符 |
| `\d` | 数字（`\D` 非数字） |
| `\w` | 字母数字下划线 |
| `\s` | 空白 |
| `*` | 0 次或多次 |
| `+` | 1 次或多次 |
| `?` | 0 次或 1 次 |
| `{n,m}` | n 到 m 次 |
| `^` | 开头 |
| `$` | 结尾 |
| `[abc]` | 字符集 |
| `( )` | 分组 |

### 常用操作

```csharp
// 匹配
Regex.IsMatch("abc123", @"\d+");        // true

// 提取所有匹配
MatchCollection matches = Regex.Matches("订单A100 订单B200", @"订单[A-Z]\d+");
foreach (Match m in matches) Console.WriteLine(m.Value);

// 替换
string masked = Regex.Replace("我的电话 13812345678", @"\d{11}", "***");
Console.WriteLine(masked);   // 我的电话 ***
```

### 例子：提取所有数字

```csharp
string text = "温度 23.5 度，湿度 60%";
foreach (Match m in Regex.Matches(text, @"\d+\.?\d*"))
    Console.WriteLine(m.Value);
```

### 验证手机号

```csharp
static bool IsPhone(string s) =>
    Regex.IsMatch(s, @"^1[3-9]\d{9}$");

Console.WriteLine(IsPhone("13812345678"));   // True
```

### 性能提示

频繁用同一正则时，预编译 `new Regex(pattern, RegexOptions.Compiled)` 复用。

## 核心重点
- `Regex.IsMatch/Match/Matches/Replace` 四个核心 API
- `@"..."` 原始字符串写正则，避免转义地狱
- 常用元字符：`\d` `\w` `+` `*` `?` `^` `$`
- 验证邮箱、手机号、身份证都靠正则
- 频繁用同一正则时 Compile 复用

## 练习/任务
1. 写正则验证邮箱（简单版即可）
2. 从一段文本里提取所有手机号
3. 把文本里所有连续空格替换成单个空格
4. （选做）写正则判断字符串是否是合法 IPv4 地址

## 今日刷题
- **LeetCode 125 验证回文串**：只考虑字母数字，忽略大小写。思路：双指针，跳过非字母数字。
- **LeetCode 392 判断子序列**：s 是否是 t 的子序列。思路：双指针贪心匹配。

## 打卡清单
- [ ] 会用 IsMatch 做验证
- [ ] 会用 Matches 提取所有匹配
- [ ] 会用 Replace 替换敏感信息
- [ ] 已完成至少一道今日刷题
