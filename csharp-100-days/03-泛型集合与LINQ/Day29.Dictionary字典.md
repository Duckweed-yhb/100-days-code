# Day 29 · Dictionary字典

## 今日目标
- 掌握键值对集合 `Dictionary<TKey, TValue>`
- 学会按键查找、遍历、判存在
- 理解为什么它查找是 O(1)

## 学习内容

### Dictionary 是什么

按键（Key）存值（Value），按键查值非常快：

```csharp
Dictionary<string, int> score = new();
score["余泓彬"] = 95;
score["张三"] = 80;
score["李四"] = 72;

Console.WriteLine(score["余泓彬"]);   // 95
```

### 初始化与常用操作

```csharp
Dictionary<string, string> capitals = new()
{
    ["中国"] = "北京",
    ["日本"] = "东京",
    ["英国"] = "伦敦"
};

Console.WriteLine(capitals["中国"]);       // 北京
capitals["法国"] = "巴黎";                   // 加新键
Console.WriteLine(capitals.Count);           // 4

// 安全取值：键不存在时 TryGetValue 不抛异常
if (capitals.TryGetValue("德国", out string? city))
    Console.WriteLine(city);
else
    Console.WriteLine("没有德国");

capitals.Remove("英国");      // 按键删除
capitals.ContainsKey("法国");  // true
capitals.Clear();
```

### 遍历

```csharp
foreach (KeyValuePair<string, string> kv in capitals)
{
    Console.WriteLine($"{kv.Key} -> {kv.Value}");
}

// 只要键 / 只要值
foreach (string country in capitals.Keys) Console.WriteLine(country);
foreach (string city in capitals.Values) Console.WriteLine(city);
```

### 为什么查找快

Dictionary 内部用哈希表：对 Key 算个哈希码，直接定位到桶。平均查找、插入、删除都是 O(1)。如果用 List 挨个找，是 O(n)。

### 例子：词频统计

```csharp
string text = "the cat sat on the mat the cat";
string[] words = text.Split(' ');

Dictionary<string, int> freq = new();
foreach (string w in words)
{
    if (freq.ContainsKey(w)) freq[w]++;
    else freq[w] = 1;
}

foreach (var kv in freq)
    Console.WriteLine($"{kv.Key}: {kv.Value}");
```

## 核心重点
- `Dictionary<TKey, TValue>` 键值对集合，键唯一
- `dict[key] = value` 写入；`dict[key]` 读取（键不存在会抛）
- `TryGetValue` 安全取值；`ContainsKey` 判存在
- 遍历用 KeyValuePair；Keys/Values 单独取
- 查找 O(1)，别再用 List 硬搜

## 练习/任务
1. 用 Dictionary 实现通讯录：姓名→电话，支持查询、添加、删除
2. 写程序统计一段英文里每个单词出现次数
3. 用 Dictionary 重做昨天的两数之和：一遍遍历，目标差是否已出现
4. （选做）统计一篇文章中每个字母出现次数，按次数从高到低输出

## 今日刷题
- **LeetCode 1 两数之和（Dictionary 优化版）**：遍历数组，对每个元素 x，查 target-x 是否在字典里；在就返回两个下标，不在就把 x 存进字典。一遍 O(n)。
- **LeetCode 242 有效的字母异位词**：两字符串字母组成是否相同。思路：用 Dictionary<char,int> 计数，s 加一、t 减一，最后所有计数为 0 即合法。

## 打卡清单
- [ ] 会用 Dictionary 增删查
- [ ] 会用 TryGetValue 安全取值
- [ ] 会做词频统计
- [ ] 已用 Dictionary 重做两数之和
