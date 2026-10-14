# Day 42 · 异常基础try-catch

## 今日目标
- 理解异常是程序运行错误的标准处理机制
- 掌握 try / catch / finally
- 学会用异常做错误处理而不是 if 判断

## 学习内容

### 什么是异常

程序运行时遇到错误（除以零、文件不存在、输入格式错），就**抛**一个异常。不处理的话程序崩溃。C# 用 try-catch 捕获：

```csharp
try
{
    Console.Write("输入一个数：");
    int n = int.Parse(Console.ReadLine()!);
    Console.WriteLine($"100 / {n} = {100 / n}");
}
catch (DivideByZeroException)
{
    Console.WriteLine("不能除以零");
}
catch (FormatException)
{
    Console.WriteLine("输入的不是整数");
}
finally
{
    Console.WriteLine("这段总执行，不管有没有异常");
}
```

### 执行流程

1. try 块正常执行完 → 跳过所有 catch，执行 finally；
2. try 里抛异常 → 立刻跳出 try，找匹配的 catch；
3. catch 处理完 → 执行 finally；
4. 没匹配的 catch → 异常往上抛，直到崩溃。

### 常见内置异常

| 异常 | 触发场景 |
|---|---|
| `DivideByZeroException` | 整数除以零 |
| `FormatException` | int.Parse("abc") |
| `NullReferenceException` | 用 null 调成员 |
| `IndexOutOfRangeException` | 数组越界 |
| `ArgumentNullException` | 参数传 null |
| `IOException` | 文件读写失败 |

### 不要吞异常

```csharp
// 错例：什么都不做，错误被埋了
try { ... } catch { }

// 对例：要么处理，要么记日志，要么往上抛
try { ... }
catch (IOException ex)
{
    Console.WriteLine($"文件错：{ex.Message}");
}
```

### 用异常做控制流？别

```csharp
// 别这样：用异常判断输入是否合法
try { int n = int.Parse(s); 用它; }
catch { 提示; }

// 应该：
if (int.TryParse(s, out int n)) 用它;
else 提示;
```

异常代价大，正常流程别依赖它。

## 核心重点
- try 里放可能出错的代码
- catch 按类型匹配，先具体后一般
- finally 不管有没有异常都执行（释放资源）
- 别空 catch 吞错误
- 正常判断用 if/TryParse，别用异常做流程

## 练习/任务
1. 写一个安全除法：输入两个数，捕获除零与格式错
2. 读用户输入年龄，捕获格式错，提示"请输入数字"
3. 故意写一段会抛 NullReferenceException 的代码，用 catch 捕获并打印消息
4. （选做）把 Day05 的 TryParse 版本改成 try-catch 版本，对比哪种更自然

## 今日刷题
- **LeetCode 88 合并两个有序数组**：nums1 尾部留够空间，从后往前合并。思路：三指针 i、j、k 从尾部开始写。
- **LeetCode 66 加一**：大整数加一。思路：从末位加，逢 9 变 0 进位；最高位还进位则数组扩容。

## 打卡清单
- [ ] 会写 try-catch-finally
- [ ] 会按异常类型分别处理
- [ ] 知道别空 catch
- [ ] 已完成至少一道今日刷题
