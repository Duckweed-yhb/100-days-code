# Day 40 · Lambda表达式

## 今日目标
- 掌握 Lambda 写法 `(参数) => 表达式`
- 理解闭包：Lambda 捕获外部变量
- 在 LINQ 与事件里熟练使用

## 学习内容

### Lambda 是什么

Lambda 就是"匿名方法"的简写，专门喂给委托：

```csharp
Func<int, int> square = n => n * n;
//                       ↑  参数  =>  表达式

Func<int, int, int> add = (a, b) => a + b;

Action<string> greet = name => Console.WriteLine($"你好，{name}");
```

读法：`n => n * n` 即"给我 n，返回 n 乘 n"。

### 多语句 Lambda

```csharp
Func<int, int, int> div = (a, b) =>
{
    if (b == 0) throw new DivideByZeroException();
    return a / b;
};
```

### 类型推断

编译器从委托类型推断参数类型，不用写 `(int n) =>`。

### 闭包：捕获外部变量

Lambda 可以"记住"它定义时所在作用域的变量：

```csharp
int factor = 10;
Func<int, int> multiply = x => x * factor;

Console.WriteLine(multiply(5));    // 50
factor = 20;
Console.WriteLine(multiply(5));    // 100！Lambda 捕获的是变量本身，不是当时的值
```

这叫闭包。要小心：捕获的变量被外部改了，Lambda 里看到的也跟着变。

### Lambda 在 LINQ 里

```csharp
var result = students
    .Where(s => s.Score >= 60)
    .OrderByDescending(s => s.Score)
    .Select(s => s.Name);
```

每个 `=>` 都是一个 Lambda，本质是委托。

### 在事件里

```csharp
button.Click += (sender, e) => Console.WriteLine("按钮被点了");
```

## 核心重点
- Lambda：`(参数) => 表达式或 { 语句块 }`
- 编译器自动推断参数类型
- Lambda 捕获外部变量，形成闭包
- 闭包捕获的是变量本身，不是快照
- LINQ、事件、回调全是 Lambda 的天下

## 练习/任务
1. 用 Lambda 写一个乘法表打印器
2. 写一个方法 `Repeat(int n, Action action)`，把 action 执行 n 次
3. 观察闭包：在循环里把 Lambda 存进 List，循环结束后调用，看输出是什么
4. （选做）用 Lambda 实现一个简单的事件处理器：数字变化时打印

## 今日刷题
- **LeetCode 27 移除元素**：原地删除所有等于 val 的元素。思路：双指针，慢指针指写位置，扫到非 val 就写过去。
- **LeetCode 26 删除有序数组重复项**：原地去重。思路：慢指针指唯一位置，遇到与前一个不同的就写过去。

## 打卡清单
- [ ] 会写单语句与多语句 Lambda
- [ ] 理解闭包捕获变量
- [ ] 在 LINQ 里熟练写 Lambda
- [ ] 已完成至少一道今日刷题
