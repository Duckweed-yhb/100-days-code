# Day 32 · Stack栈

## 今日目标
- 掌握 `Stack<T>` 后进先出（LIFO）
- 理解栈在表达式求值、括号匹配中的应用
- 区分栈与队列的使用场景

## 学习内容

### Stack 是什么

栈像一叠盘子：最后放上去的最先拿下来。

```csharp
Stack<int> s = new();
s.Push(1);
s.Push(2);
s.Push(3);

Console.WriteLine(s.Peek());   // 看栈顶：3
Console.WriteLine(s.Pop());    // 弹出：3
Console.WriteLine(s.Pop());    // 2
Console.WriteLine(s.Count);    // 1
```

### 常用操作

| 方法 | 作用 |
|---|---|
| `Push(x)` | 压栈 |
| `Pop()` | 弹栈顶，空栈抛异常 |
| `Peek()` | 看栈顶不弹 |
| `Count` | 元素数 |

### 经典应用：括号匹配

```csharp
static bool IsValid(string s)
{
    Stack<char> stack = new();
    foreach (char c in s)
    {
        if (c == '(' || c == '[' || c == '{') stack.Push(c);
        else
        {
            if (stack.Count == 0) return false;
            char top = stack.Pop();
            if (c == ')' && top != '(') return false;
            if (c == ']' && top != '[') return false;
            if (c == '}' && top != '{') return false;
        }
    }
    return stack.Count == 0;
}

Console.WriteLine(IsValid("({[]})"));    // True
Console.WriteLine(IsValid("([)]"));      // False
```

### 调用栈为什么叫栈

函数 A 调 B，B 调 C：C 先返回，再 B 返回，再 A 返回——这就是 LIFO。程序崩溃时看到的"堆栈跟踪"就是栈。

### Stack vs Queue

| 结构 | 顺序 | 例子 |
|---|---|---|
| Stack | 后进先出 LIFO | 括号匹配、撤销、函数调用 |
| Queue | 先进先出 FIFO | 排队、BFS、任务调度 |

## 核心重点
- Stack\<T> Push/Pop/Peek，LIFO
- 括号匹配、表达式求值、撤销操作都是栈
- Pop 空栈抛异常，先判 Count
- 与 Queue 对称，别混
- 函数调用本身就是栈

## 练习/任务
1. 用 Stack 判断一句话里的括号是否匹配
2. 用两个 Stack 实现一个队列（入队栈、出队栈）
3. 写方法 `DecimalToBinary(int n)` 用栈把十进制转二进制
4. （选做）实现一个简单的"撤销"功能：每次输入命令压栈，输入 undo 弹栈打印

## 今日刷题
- **LeetCode 20 有效的括号**：今天正文的原题。C# 用 Stack\<char>，左括号压栈，右括号检查是否与栈顶匹配。
- **LeetCode 155 最小栈**：设计一个栈，除了 push/pop/top 还要能 O(1) 返回最小值。思路：用两个栈，主栈正常压，辅助栈每次压当前最小值。

## 打卡清单
- [ ] 会用 Stack Push/Pop/Peek
- [ ] 理解 LIFO
- [ ] 写出括号匹配算法
- [ ] 已完成至少一道今日刷题
