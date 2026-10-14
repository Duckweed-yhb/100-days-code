# Day 27 · 泛型约束

## 今日目标
- 知道为什么要给 T 加约束
- 掌握 `where T : ...` 常见约束
- 能在泛型代码里安全调用 T 的方法

## 学习内容

### 没有约束时 T 能做什么

```csharp
static void Print<T>(T x)
{
    Console.WriteLine(x.ToString());    // ToString 是 object 的方法，所以能调
    // x.CompareTo(...);                // 错：T 不一定有 CompareTo
}
```

编译器只知道 T 是 object，所以只能调 object 有的方法（Equals、GetHashCode、ToString）。要让 T 支持更多操作，加约束。

### 常见约束

```csharp
// T 必须实现 IComparable<T>，才能比较
static T Max<T>(T a, T b) where T : IComparable<T>
{
    return a.CompareTo(b) >= 0 ? a : b;
}

// T 必须是引用类型
static void ReferenceMethod<T>(T x) where T : class { }

// T 必须是值类型
static void ValueMethod<T>(T x) where T : struct { }

// T 必须有无参构造函数，才能 new T()
static T CreateNew<T>() where T : new() => new T();

// T 必须是 Person 或其子类
static void PrintName<T>(T p) where T : Person => Console.WriteLine(p.Name);
```

### 约束组合

```csharp
static T DoSomething<T>(T x)
    where T : Person, IComparable<T>, new()
{
    return new T();
}
```

### 实例：求数组最大值

```csharp
static T MaxOfArray<T>(T[] arr) where T : IComparable<T>
{
    if (arr.Length == 0) throw new ArgumentException("数组空");
    T max = arr[0];
    foreach (T x in arr)
        if (x.CompareTo(max) > 0) max = x;
    return max;
}

Console.WriteLine(MaxOfArray(new[] { 3, 7, 2 }));          // 7
Console.WriteLine(MaxOfArray(new[] { "b", "a", "c" }));     // c
```

## 核心重点
- 约束告诉编译器"T 至少是什么"，从而能调更多方法
- `where T : IComparable<T>` 让 T 可比较
- `class` / `struct` 约束区分引用/值类型
- `new()` 约束允许在泛型里 new T()
- 约束越多，泛型越"挑"，但能力越强

## 练习/任务
1. 写泛型方法 `MinOfArray<T>` 求最小值（加 IComparable 约束）
2. 写泛型方法 `SwapIfGreater<T>`：若 a>b 则交换，加 IComparable 约束
3. 写泛型类 `Repository<T>` where T : new()，方法 `Create()` 返回 new T()
4. （选做）把 Day26 的 First<T> 加上"数组非空"检查，返回值用 `T?` 表达可能为 null

## 今日刷题
- **LeetCode 704 二分查找**：有序数组找 target。思路：左右指针 while (l<=r)，mid=(l+r)/2，比大小收缩区间。C# 写一个泛型 `int BinarySearch<T>(T[] a, T t) where T : IComparable<T>`，体会约束的用途。
- **LeetCode 35 搜索插入位置**：二分找不到时返回该插入位置。思路同上，循环结束时 l 就是插入点。

## 打卡清单
- [ ] 会写 where T : 接口约束
- [ ] 理解 class/struct/new() 约束含义
- [ ] 会用约束在泛型里调用 T 的方法
- [ ] 已完成至少一道今日刷题
