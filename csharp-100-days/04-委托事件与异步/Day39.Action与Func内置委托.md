# Day 39 · Action与Func内置委托

## 今日目标
- 认识 .NET 内置的 Action / Func / Predicate
- 不再为每个签名都自定义委托
- 能看懂 BCL 里到处都是的委托参数

## 学习内容

### 为什么要内置委托

每次写 `delegate int Op(int,int)` 太啰嗦。.NET 提供了通用版本：

| 委托 | 签名 | 用途 |
|---|---|---|
| `Action` | `void()` | 无参无返回 |
| `Action<T1>` | `void(T1)` | 一个参数无返回 |
| `Action<T1,T2>` | `void(T1,T2)` | 两个参数无返回 |
| `Func<TResult>` | `TResult()` | 无参有返回 |
| `Func<T,TResult>` | `TResult(T)` | 一个参数有返回 |
| `Predicate<T>` | `bool(T)` | 判断真假 |

### Action：无返回

```csharp
Action<string> print = Console.WriteLine;
print("hello");

List<int> nums = new() { 1, 2, 3 };
nums.ForEach(n => Console.WriteLine(n * 2));   // ForEach 接收 Action<int>
```

### Func：有返回

```csharp
Func<int, int, int> add = (a, b) => a + b;
Console.WriteLine(add(3, 5));   // 8

Func<string, int> length = s => s.Length;
Console.WriteLine(length("hello"));   // 5
```

最后一个泛型参数永远是返回值类型。

### Predicate：判断

```csharp
Predicate<int> isEven = n => n % 2 == 0;
Console.WriteLine(isEven(4));   // True
```

> LINQ 的 `Where` 接收的其实就是 `Func<T, bool>`，与 `Predicate<T>` 等价但更通用。

### 实战：用 Action/Func 重构

```csharp
// 之前手写委托
delegate int Op(int a, int b);

// 现在直接用 Func<int,int,int>
static int Compute(int a, int b, Func<int, int, int> op) => op(a, b);

Console.WriteLine(Compute(10, 3, (x, y) => x + y));
```

## 核心重点
- Action\<T...>：无返回值的方法
- Func\<T...,TResult>：有返回值，最后一个泛型是返回类型
- Predicate\<T>：返回 bool，常用于过滤
- 日常别再自定义委托类型，直接用 Action/Func
- 这些都是类型，能当参数、能当字段

## 练习/任务
1. 用 `Func<int,int,int>` 写一个计算器，传加减乘除四种运算
2. 用 `Action<string>` 写一个日志方法，可同时挂屏幕与文件输出
3. 用 `Predicate<int>` 做一个 List 过滤工具
4. （选做）把 Day35 的 Where 用 `Func<T,bool>` 手写一遍

## 今日刷题
- **LeetCode 206 反转链表（委托版）**：把节点比较、打印都抽成 `Action<ListNode>` 与 `Func<ListNode,bool>`。
- **LeetCode 83 删除排序链表重复元素**：遍历链表，若当前与下一个值相同就跳过下一个。C# 用 while 走 cur。

## 打卡清单
- [ ] 会用 Action 与 Func 写委托变量
- [ ] 能说出 Func 最后一个泛型是返回类型
- [ ] 不再自定义简单委托
- [ ] 已完成至少一道今日刷题
