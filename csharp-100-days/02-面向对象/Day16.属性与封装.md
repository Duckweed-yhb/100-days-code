# Day 16 · 属性与封装

## 今日目标
- 理解为什么要封装，不要把字段 public 出去
- 掌握属性（property）的写法：完整属性、自动属性
- 用属性加校验规则

## 学习内容

### 为什么需要封装

上一天我们直接 `public string Name`，外面随便改：

```csharp
Student s = new Student("余泓彬", 20);
s.Age = -5;        // 合法？？？年龄怎么可能负的
```

封装原则：**字段私有化，通过属性暴露受控的访问**。

### 完整属性（带校验）

```csharp
class Student
{
    private string name = "";      // 私有字段，外面摸不到
    private int age;

    public string Name            // 属性：像字段一样用，背后是方法
    {
        get { return name; }
        set { name = value; }     // value 是隐式参数，表示要赋的值
    }

    public int Age
    {
        get { return age; }
        set
        {
            if (value < 0 || value > 150)
                throw new ArgumentOutOfRangeException(nameof(value), "年龄不合法");
            age = value;
        }
    }
}

Student s = new Student();
s.Name = "余泓彬";       // 调用 set
Console.WriteLine(s.Name);   // 调用 get
// s.Age = -5;           // 抛异常！
```

### 自动属性

不需要额外校验时，C# 提供自动属性，背后自动藏一个字段：

```csharp
class Student
{
    public string Name { get; set; } = "未命名";    // 自动属性 + 初始值
    public int Age { get; set; }
    public double Score { get; private set; }      // 外面能读不能写
}
```

`Score { get; private set; }` 表示外部可读、只能在类内部写，很常用。

### 只读属性

```csharp
public string Address { get; init; }   // 只能在创建对象初始化时设
// 或
public string FullName => $"{LastName} {FirstName}";   // 计算属性，没有 set
```

### 封装的好处

| 好处 | 说明 |
|---|---|
| 校验 | 非法值进不来 |
| 可控 | 可以只让读、不让写 |
| 可改实现 | 内部换存储方式，外部调用代码不变 |
| 可加日志/副作用 | set 里顺便记日志、发事件 |

## 核心重点
- 字段用 `private` 藏起来，用 `public` 属性暴露
- `get` 读、`set` 写，`value` 是待赋值
- 不需要校验就用自动属性 `{ get; set; }`
- `{ get; private set; }` 做只读对外属性
- 封装 = 隐藏内部细节，暴露受控接口

## 练习/任务
1. 把上一天 Student 的 Age 改成属性，加 0~150 校验
2. 写 `BankAccount` 类：余额私有，提供 Deposit(存钱)、Withdraw(取钱) 方法，余额不能为负
3. 写 `Temperature` 类：内部存摄氏，提供 `Celsius` 与 `Fahrenheit` 两个计算属性互转
4. （选做）给 `Name` 属性的 set 加规则：不能为空，否则抛异常

## 打卡清单
- [ ] 理解为什么字段要 private
- [ ] 会写带校验的完整属性
- [ ] 会用自动属性简化代码
- [ ] 已写出 BankAccount 受控余额
