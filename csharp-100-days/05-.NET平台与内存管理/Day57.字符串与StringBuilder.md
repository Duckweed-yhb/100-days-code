# Day 57 · 字符串与StringBuilder

## 今日目标
- 深入理解字符串不可变性的代价
- 掌握 `StringBuilder` 高效拼接
- 知道什么时候用 string 相加、什么时候用 StringBuilder

## 学习内容

### 字符串不可变的代价

```csharp
string s = "";
for (int i = 0; i < 10000; i++)
{
    s += i.ToString();
}
```

每 `+=` 一次都**新建一个字符串**：分配新内存、复制旧内容、加上新内容。循环 10000 次就产生 10000 个临时字符串，GC 压力巨大。

### StringBuilder：可变字符串

```csharp
using System.Text;

StringBuilder sb = new StringBuilder();
for (int i = 0; i < 10000; i++)
{
    sb.Append(i);
}
string result = sb.ToString();
```

StringBuilder 内部维护一个可扩容的字符数组，Append 直接往里写，不产生新字符串。

### 常用方法

```csharp
StringBuilder sb = new StringBuilder("你好");
sb.Append("，世界");        // 追加
sb.AppendLine("！");         // 追加并换行
sb.AppendFormat("数字 {0}", 42);
sb.Replace("世界", "C#");    // 替换
sb.Length = 0;              // 清空
string final = sb.ToString();
```

### 什么时候用哪个

| 场景 | 选 |
|---|---|
| 几次拼接 | `string +` 或插值 |
| 循环里大量拼接 | `StringBuilder` |
| 简单格式化 | `$"..."` |
| 解析/操作已有字符串 | string 方法 |

### 其他字符串技巧

```csharp
string raw = """
    这是
    多行
    原始字符串
    """;
// C# 11 三引号原始字符串，所见即所得
```

## 核心重点
- string 不可变，每次 += 都新建对象
- StringBuilder 适合循环里大量拼接
- 少量拼接直接 `+` 或插值即可
- 三引号原始字符串写多行文本很方便
- 性能敏感处先 Profiling 再优化

## 练习/任务
1. 对比 `+=` 与 `StringBuilder` 拼 10 万次字符串的耗时
2. 用 StringBuilder 生成一个九九乘法表文本
3. 用三引号原始字符串写一段多行 SQL
4. （选做）查 string.Create 与 string.Join 的性能

## 今日刷题
- **LeetCode 14 最长公共前缀**：纵向扫描，按列比。
- **LeetCode 6 Z 字形变换**：按行读。思路：用 StringBuilder 数组按行收集字符。

## 打卡清单
- [ ] 理解字符串不可变代价
- [ ] 会用 StringBuilder 拼接
- [ ] 知道什么时候用哪个
- [ ] 已完成至少一道今日刷题
