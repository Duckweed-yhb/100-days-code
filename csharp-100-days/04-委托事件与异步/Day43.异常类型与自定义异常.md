# Day 43 · 异常类型与自定义异常

## 今日目标
- 理解异常继承体系
- 学会 `throw` 主动抛异常
- 会写自定义异常类型

## 学习内容

### 异常继承体系

所有异常都继承自 `System.Exception`：

```
Exception
  ├── SystemException（CLR 抛）
  │     ├── ArgumentException
  │     │     └── ArgumentNullException
  │     ├── FormatException
  │     ├── InvalidOperationException
  │     └── IndexOutOfRangeException
  └── ApplicationException（自定义，不推荐直接继承）
```

catch 时先抓具体的，再抓一般的：

```csharp
try { ... }
catch (ArgumentNullException ex) { ... }   // 先具体
catch (ArgumentException ex) { ... }       // 后一般
catch (Exception ex) { ... }               // 最后兜底
```

顺序反了编译报错（更一般的在前面永远先匹配）。

### throw 主动抛

```csharp
static void SetAge(int age)
{
    if (age < 0 || age > 150)
        throw new ArgumentOutOfRangeException(nameof(age), "年龄不合法");
}
```

在方法里检查参数，不合法就抛——让调用方处理。

### 重新抛出异常

```csharp
try { Risky(); }
catch (IOException ex)
{
    Console.WriteLine($"记录日志：{ex.Message}");
    throw;          // 原封不动往上抛，保留堆栈
    // 不要写成 throw ex; 会丢堆栈！
}
```

### 自定义异常

业务错误用自定义异常更清晰：

```csharp
public class InsufficientBalanceException : Exception
{
    public decimal Balance { get; }
    public decimal Amount { get; }

    public InsufficientBalanceException(decimal balance, decimal amount)
        : base($"余额 {balance} 不足，无法取 {amount}")
    {
        Balance = balance;
        Amount = amount;
    }
}

class BankAccount
{
    public decimal Balance { get; private set; }
    public void Withdraw(decimal amount)
    {
        if (amount > Balance)
            throw new InsufficientBalanceException(Balance, amount);
        Balance -= amount;
    }
}
```

```csharp
try { account.Withdraw(10000); }
catch (InsufficientBalanceException ex)
{
    Console.WriteLine(ex.Message);
}
```

## 核心重点
- 异常都继承 Exception；catch 先具体后一般
- `throw new XxxException(...)` 主动抛
- 重抛用 `throw;` 不要 `throw ex;`
- 业务错误用自定义异常，带业务数据
- 异常信息要让人看得懂

## 练习/任务
1. 写 `Divide` 方法，除数为负时抛 `ArgumentOutOfRangeException`
2. 自定义 `StudentNotFoundException`，在按学号查学生时抛出
3. 写一段代码：catch 后记日志，再 `throw;` 重抛
4. （选做）查 `ExceptionFilter`（`catch when`）怎么用

## 今日刷题
- **LeetCode 21 合并两个有序链表**：递归或迭代，每次选较小节点接结果链表。思路：dummy 头节点 + 尾指针。
- **LeetCode 2 两数相加**：两个逆序链表代表大数，相加。思路：同步走两链表，处理进位，长度不齐补 0。

## 打卡清单
- [ ] 会主动 throw 异常
- [ ] 会写自定义异常
- [ ] 理解 throw 与 throw ex 的区别
- [ ] 已完成至少一道今日刷题
