# Day 49 · 扩展方法

## 今日目标
- 学会用扩展方法给现有类型"加方法"
- 理解 LINQ 为什么能写成 `list.Where(...)`
- 写自己的字符串/集合扩展

## 学习内容

### 什么是扩展方法

LINQ 的 `Where` 明明是 `Enumerable` 类的静态方法，为什么能写成 `list.Where(...)`？答案就是扩展方法。

扩展方法允许你在不改原类的情况下，给它"加"方法：

```csharp
public static class StringExtensions
{
    // this string s 就是"扩展谁"
    public static bool IsPalindrome(this string s)
    {
        Span<char> reversed = stackalloc char[s.Length];
        for (int i = 0; i < s.Length; i++)
            reversed[i] = s[s.Length - 1 - i];
        return s.AsSpan().SequenceEqual(reversed);
    }
}

// 用起来像 string 自带方法
string word = "abcba";
Console.WriteLine(word.IsPalindrome());   // True
```

### 规则

1. 必须定义在**静态类**里；
2. 第一个参数加 `this` 修饰，表示扩展的类型；
3. 调用时像实例方法，但本质是静态调用。

### 常用例子

```csharp
public static class CollectionExtensions
{
    public static void Print<T>(this IEnumerable<T> items, string sep = ", ")
    {
        Console.WriteLine(string.Join(sep, items));
    }
}

new[] { 1, 2, 3 }.Print();       // 1, 2, 3
```

### LINQ 就是一堆扩展方法

`Where`、`Select`、`OrderBy` 都是 `System.Linq.Enumerable` 里对 `IEnumerable<T>` 的扩展方法。这就是为什么 `using System.Linq;` 后就能 `list.Where(...)`。

### 别滥用扩展方法

- 别给 object 加扩展，到处可见很难维护；
- 别用扩展方法假装"有"某个方法而掩盖设计问题；
- 优先组合、封装类，扩展方法只做"看起来像原类方法"的小工具。

## 核心重点
- 扩展方法：静态类里 `this T x` 第一个参数
- 调用像实例方法，本质是静态调用
- LINQ 全靠扩展方法实现
- 适合给简单类型加小工具方法
- 别滥用，别给 object 加

## 练习/任务
1. 写扩展方法 `IsEven(this int n)`，调用 `4.IsEven()` 返回 true
2. 写扩展方法 `WordCount(this string s)` 返回单词数
3. 写扩展方法 `TakeLast<T>(this IEnumerable<T> src, int n)` 取最后 n 个
4. （选做）给 string 写 `ToTitleCase`：每个单词首字母大写

## 今日刷题
- **LeetCode 145 二叉树后序遍历**：迭代用两个栈或一个栈+逆序。今天写迭代版。
- **LeetCode 100 相同二叉树**：两棵树是否完全相同。思路：递归同步走。

## 打卡清单
- [ ] 会写扩展方法
- [ ] 理解 LINQ 是扩展方法
- [ ] 知道扩展方法的限制
- [ ] 已完成至少一道今日刷题
