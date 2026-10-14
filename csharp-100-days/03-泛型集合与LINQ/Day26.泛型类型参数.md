# Day 26 · 泛型类型参数

## 今日目标
- 理解为什么需要泛型：类型安全 + 不重复写
- 学会写简单的泛型方法与泛型类
- 知道 `T` 这种占位符怎么工作

## 学习内容

### 没有泛型的世界

假设要做一个"能存一个东西的盒子"：

```csharp
class IntBox { public int Value; }
class StringBox { public string Value; }
class DoubleBox { public double Value; }
// 每种类型写一遍，恶心
```

或者用 object：

```csharp
class ObjectBox { public object Value; }
ObjectBox b = new() { Value = 123 };
int x = (int)b.Value;     // 要强制转换；存字符串再转 int 就崩
```

object 方案丢了类型安全。

### 泛型：类型也当参数

```csharp
class Box<T>           // T 是类型参数，调用时再定
{
    public T Value;
    public void Show() => Console.WriteLine(Value);
}

Box<int> intBox = new() { Value = 123 };
Box<string> strBox = new() { Value = "hello" };

int a = intBox.Value;       // 不用强转，编译期就知道是 int
string s = strBox.Value;
```

### 泛型方法

```csharp
static T Max<T>(T a, T b)
{
    return Comparer<T>.Default.Compare(a, b) > 0 ? a : b;
}

Console.WriteLine(Max(3, 5));            // 5
Console.WriteLine(Max("apple", "banana")); // banana
```

编译器根据实参自动推断 T，`Max(3,5)` 不用写 `Max<int>(3,5)`。

### 泛型的好处

| 好处 | 说明 |
|---|---|
| 类型安全 | 编译期就查出类型错误 |
| 无装箱拆箱 | 不像 object 那样频繁转换 |
| 代码复用 | 一套逻辑适配多种类型 |
| 性能好 | JIT 对值类型泛型有专门实现 |

### 命名约定

类型参数常用单字母：`T`（Type）、`TKey`、`TValue`、`TResult`。一个时写 `T`，多个时写有意义的名字。

## 核心重点
- 泛型让"逻辑"与"具体类型"分离
- `class Box<T>`、`T Method<T>(...)` 声明类型参数
- 使用时 `Box<int>` 指定类型，编译器自动检查
- 避免用 object 装任意类型，会丢类型安全
- `T` 只是占位符，真正的类型在调用时确定

## 练习/任务
1. 写泛型类 `Pair<T1, T2>`，装两个不同类型的值，打印出来
2. 写泛型方法 `Swap<T>(ref T a, ref T b)`，交换两个变量
3. 写泛型方法 `First<T>(T[] arr)` 返回数组第一个元素（空数组抛异常）
4. （选做）写 `Stack<T>` 雏形：内部用数组，提供 Push、Pop

## 今日刷题
- **LeetCode 217 存在重复元素**：给一组整数，判断是否有重复。思路：遍历同时用 HashSet 记录见过的数，遇到重复即返回 true。今天先了解 `HashSet<T>` 大概用法，Day30 细讲。
- **LeetCode 268 丢失的数字**：0~n 中缺一个数。思路一：求 0+1+...+n 的和减去数组和；思路二：XOR。C# 直接 LINQ 一行 `Enumerable.Range(0,n+1).Sum() - nums.Sum()`。

## 打卡清单
- [ ] 理解泛型解决了什么问题
- [ ] 会写简单泛型类与泛型方法
- [ ] 会用类型参数 T 写通用逻辑
- [ ] 已完成至少一道今日刷题
