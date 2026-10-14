# Day 11 · 数组基础

## 今日目标
- 掌握一维数组的声明、初始化与遍历
- 理解下标从 0 开始、越界异常
- 会用 `foreach` 与常用数组方法

## 学习内容

### 声明与初始化

```csharp
int[] a = new int[5];                 // 长度 5，默认全是 0
int[] b = new int[] { 1, 2, 3, 4, 5 }; // 初始化器
int[] c = { 10, 20, 30 };             // 简化写法
```

> 注意类型是 `int[]`（数组），不是 `int a[5]`（那是 C/C++）。

### 访问元素

下标从 0 开始：

```csharp
int[] scores = { 90, 85, 77, 92, 88 };
Console.WriteLine(scores[0]);     // 90（第一个）
Console.WriteLine(scores[4]);     // 88（第五个）
scores[1] = 100;                  // 修改第二个
Console.WriteLine(scores.Length); // 5（长度）
```

下标越界会抛 `IndexOutOfRangeException`：`scores[5]` 直接崩。

### 遍历数组

```csharp
int[] nums = { 3, 1, 4, 1, 5, 9, 2, 6 };

// for 按下标
for (int i = 0; i < nums.Length; i++)
{
    Console.WriteLine($"第 {i} 个是 {nums[i]}");
}

// foreach 只值遍历，不要下标时更简洁
foreach (int n in nums)
{
    Console.WriteLine(n);
}
```

### 常用操作

```csharp
int[] arr = { 5, 2, 9, 1 };
Array.Sort(arr);              // 升序排序：1,2,5,9
Array.Reverse(arr);           // 反转：9,5,2,1
Console.WriteLine(arr.Max()); // 9（LINQ 扩展，后面详讲）
Console.WriteLine(arr.Sum()); // 17
Array.Clear(arr);             // 全部清零
```

### 求最大值、求和手搓一遍

```csharp
int[] data = { 33, 12, 78, 45, 90, 6 };
int max = data[0];
int sum = 0;
foreach (int x in data)
{
    if (x > max) max = x;
    sum += x;
}
Console.WriteLine($"最大 {max}，和 {sum}，平均 {(double)sum / data.Length:F2}");
```

### 二维数组（了解）

```csharp
int[,] grid = new int[3, 4];   // 3 行 4 列
grid[0, 0] = 1;
int[,] m = { {1,2}, {3,4}, {5,6} };
Console.WriteLine(m[2, 1]);     // 6
```

## 核心重点
- 数组类型写作 `int[]`，下标从 0 开始
- `Length` 取长度；越界抛异常
- `for` 按下标，`foreach` 只取值
- `Array.Sort` / `Reverse` 排序反转
- 最大值、求和、平均先手搓，再用 LINQ

## 练习/任务
1. 读入 5 个学生成绩存数组，输出最高、最低、平均分
2. 把数组 `{1,2,3,4,5}` 反转后打印（不要用 Array.Reverse，自己换位置）
3. 找出数组中出现次数最多的元素（提示：两层循环计数）
4. （选做）生成 10 个 1~100 的随机数，冒泡排序后输出

## 打卡清单
- [ ] 会声明、初始化、访问一维数组
- [ ] 会用 for 与 foreach 两种遍历
- [ ] 手搓过求最大值与求和
- [ ] 理解下标越界异常
