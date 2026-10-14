# Day 83 · 断言与测试组织

## 今日目标
- 掌握常用 Assert
- 用 [Theory] 做参数化测试
- 理解测试项目结构

## 学习内容

### 常用断言

```csharp
Assert.Equal(8, result);            // 值相等
Assert.True(x > 0);
Assert.Null(obj);
Assert.NotNull(obj);
Assert.Contains("abc", list);
Assert.Throws<Exception>(() => ...);
Assert.Empty(list);
```

### [Theory] 参数化

一组数据测同一个逻辑：

```csharp
[Theory]
[InlineData(1, 2, 3)]
[InlineData(-1, 1, 0)]
[InlineData(0, 0, 0)]
public void Add_各种情况(int a, int b, int expected)
{
    var calc = new Calculator();
    Assert.Equal(expected, calc.Add(a, b));
}
```

一个测试方法跑三遍，分别用三组数据。

### 测试项目结构

```
MyApp/
  MyApp.csproj
  Calculator.cs
MyApp.Tests/
  MyApp.Tests.csproj
  CalculatorTests.cs
```

一个源项目对应一个测试项目。

### AAA 模式

每个测试三段：

- **Arrange**：准备对象与输入；
- **Act**：调被测方法；
- **Assert**：验证结果。

```csharp
[Fact]
public void Add_3加5_等于8()
{
    // Arrange
    var calc = new Calculator();
    // Act
    int r = calc.Add(3, 5);
    // Assert
    Assert.Equal(8, r);
}
```

### 测接口与 Mock

依赖接口时，测试传假实现（后面会用 Moq 库）：

```csharp
[Fact]
public void OrderService_下单_调用Repository()
{
    var repo = new FakeRepo();
    var svc = new OrderService(repo);
    svc.Checkout();
    Assert.True(repo.Saved);
}
```

## 核心重点
- Assert 常用 Equal/True/Null/Throws
- [Theory] + [InlineData] 参数化
- AAA 三段写测试
- 一个源项目对应一个测试项目
- 依赖接口才能轻松测

## 练习/任务
1. 给 Add 写 [Theory] 三组数据
2. 给 Divide 写正常情况与异常情况测试
3. 写一个测试类覆盖你昨天的业务方法
4. （选做）查 Moq 库怎么 mock 接口

## 今日刷题
- **LeetCode 274 H 指数（复习）**：排序遍历。
- **LeetCode 380 O(1) 随机集合（复习）**：Dictionary+List。

## 打卡清单
- [ ] 会写 [Theory] 参数化测试
- [ ] 会用常用 Assert
- [ ] 按 AAA 写测试
- [ ] 已完成至少一道今日刷题
