# Day 62 · 特性Attribute

## 今日目标
- 理解特性是"贴在类型/方法上的标签"
- 会用常见内置特性
- 会写自定义特性

## 学习内容

### 特性是什么

特性是给类、方法、属性贴的"元数据标签"，本身不改变代码行为，但其他工具（反射、序列化器、测试框架）会读它：

```csharp
[Obsolete("这个方法别用了，请用 NewMethod")]
public void OldMethod() { }

[Required]
public string Name { get; set; } = "";

[JsonPropertyName("user_name")]
public string UserName { get; set; } = "";
```

### 常见内置特性

| 特性 | 作用 |
|---|---|
| `[Obsolete]` | 标记过时，编译器警告 |
| `[Required]` | 数据校验必填 |
| `[Range(0,100)]` | 数值范围 |
| `[JsonPropertyName]` | JSON 字段名映射 |
| `[Authorize]` | ASP.NET Core 鉴权 |
| `[TestMethod]` | xUnit/NUnit 测试方法标记 |
| `[DllImport]` | 调用 native C 函数 |

### 自定义特性

```csharp
[AttributeUsage(AttributeTargets.Class | AttributeTargets.Method)]
public class DeveloperAttribute : Attribute
{
    public string Name { get; }
    public DeveloperAttribute(string name) { Name = name; }
}

[Developer("余泓彬")]
class OrderService { }
```

用反射读它：

```csharp
var attr = typeof(OrderService).GetCustomAttribute<DeveloperAttribute>();
Console.WriteLine(attr?.Name);   // 余泓彬
```

### 特性与反射配合

框架经常这样干：扫描程序集，找带 `[TestMethod]` 的方法跑测试；找带 `[ApiController]` 的类注册路由。

## 核心重点
- 特性是元数据标签，用 `[...]` 贴
- 本身不做事，等反射/框架来读
- Obsolete/Required/JsonPropertyName 高频
- 自定义特性继承 Attribute
- 框架（DI、测试、序列化）大量用特性

## 练习/任务
1. 给一个方法加 `[Obsolete]`，看调用时编译器警告
2. 自定义 `[Author("...")]` 特性贴在类上，用反射读出来
3. 查 `[JsonPropertyName]` 怎么影响 JSON 序列化
4. （选做）写一个命令解析器：根据方法上的 `[Command("name")]` 路由

## 今日刷题
- **LeetCode 283 移动零（复习）**：双指针。
- **LeetCode 392 判断子序列（复习）**：双指针。

## 打卡清单
- [ ] 理解特性是元数据
- [ ] 会用常见内置特性
- [ ] 会写自定义特性
- [ ] 已完成至少一道今日刷题
