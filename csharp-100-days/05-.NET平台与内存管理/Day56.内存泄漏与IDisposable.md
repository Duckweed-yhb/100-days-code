# Day 56 · 内存泄漏与IDisposable

## 今日目标
- 理解 .NET 里也会"内存泄漏"
- 掌握 `IDisposable` 与 `using` 释放资源
- 识别常见泄漏源

## 学习内容

### .NET 也会泄漏

虽然有 GC，但下面情况对象该回收却回收不掉：

- 静态集合一直 Add，从不 Remove；
- 事件订阅者没退订，发布者一直引用它；
- 非托管资源（文件句柄、数据库连接）没释放。

### IDisposable

要释放资源的类实现 `IDisposable`：

```csharp
class FileWriter : IDisposable
{
    private StreamWriter? _writer;

    public FileWriter(string path)
    {
        _writer = new StreamWriter(path);
    }

    public void WriteLine(string s) => _writer!.WriteLine(s);

    public void Dispose()
    {
        _writer?.Dispose();
        _writer = null;
    }
}
```

### using 语句：自动释放

```csharp
using (FileWriter fw = new("log.txt"))
{
    fw.WriteLine("第一条");
}   // 出作用域自动 Dispose

// 现代写法（C# 8+），不用大括号
using var fw2 = new FileWriter("log2.txt");
fw2.WriteLine("第二条");
```

`using` 保证即使抛异常也会 Dispose。

### 常见泄漏源

1. **事件不退订**：`button.Click += Handler`，Handler 里持有大对象，按钮活着就永远回收不掉；
2. **静态集合无限增长**：`static List<object> cache` 只 Add 不 Remove；
3. **未释放 IDisposable**：文件句柄耗尽、数据库连接池满。

### 事件退订

```csharp
public void Subscribe() => sensor.Changed += OnChanged;
public void Unsubscribe() => sensor.Changed -= OnChanged;   // 用完记得
```

## 核心重点
- GC 不管非托管资源，要用 IDisposable
- `using` 语句保证 Dispose 被调用
- 事件不退订会泄漏订阅者
- 静态集合无限增长会泄漏
- 实现 IDisposable 的类，用的时候一定包 using

## 练习/任务
1. 写一个实现 IDisposable 的类，用 using 测试自动释放
2. 写一个事件订阅/退订的例子，对比退订前后订阅者能否被 GC
3. 故意写一个静态 List 一直 Add，看内存涨
4. （选做）查 `WeakReference` 是什么，怎么用来检测泄漏

## 今日刷题
- **LeetCode 55 跳跃游戏**：能否到末尾。思路：贪心维护最远可达位置。
- **LeetCode 58 最后一个单词长度**：从后往前数，跳过末尾空格，再数到空格为止。

## 打卡清单
- [ ] 会实现 IDisposable
- [ ] 会用 using 自动释放
- [ ] 知道事件不退订会泄漏
- [ ] 已完成至少一道今日刷题
