# Day 82 · 单元测试xUnit入门

## 今日目标
- 理解什么是单元测试、为什么写
- 用 xUnit 建测试项目
- 写第一个测试用例

## 学习内容

### 为什么写单元测试

- 改代码不害怕：跑一遍测试就知道有没有改坏；
- 文档：测试就是"怎么用这个方法"的活文档；
- 重构有底气。

### 建测试项目

```powershell
dotnet new xunit -o MyApp.Tests
cd MyApp.Tests
dotnet add reference ../MyApp/MyApp.csproj
```

### 第一个测试

假设业务代码：

```csharp
public class Calculator
{
    public int Add(int a, int b) => a + b;
    public int Divide(int a, int b)
    {
        if (b == 0) throw new DivideByZeroException();
        return a / b;
    }
}
```

测试：

```csharp
public class CalculatorTests
{
    [Fact]
    public void Add_两数相加_正确返回和()
    {
        var calc = new Calculator();
        int result = calc.Add(3, 5);
        Assert.Equal(8, result);
    }

    [Fact]
    public void Divide_除数为零_抛异常()
    {
        var calc = new Calculator();
        Assert.Throws<DivideByZeroException>(() => calc.Divide(10, 0));
    }
}
```

### 跑测试

```powershell
dotnet test
```

### 命名约定

方法名 `方法名_场景_期望`：`Add_正数负数_正确`。一看就知道测什么。

## 核心重点
- 单元测试验证最小单元
- xUnit 用 [Fact] 标记测试方法
- Arrange-Act-Assert 三段
- Assert.Equal 比较值
- dotnet test 一键跑全部

## 练习/任务
1. 建测试项目，给 Calculator 写 3 个测试
2. 测 Add 负数、零
3. 测 Divide 正常情况
4. （选做）查 `[Theory]` 与 `[InlineData]` 参数化测试

## 今日刷题
- **LeetCode 169 多数元素（复习）**：Boyer-Moore。
- **LeetCode 88 合并两个有序数组（复习）**：从后往前。

## 打卡清单
- [ ] 建过 xUnit 测试项目
- [ ] 会写 [Fact] 测试
- [ ] 会用 Assert.Equal
- [ ] 已完成至少一道今日刷题
