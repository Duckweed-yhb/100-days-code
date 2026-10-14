# Day 23 · 多接口与显式实现

## 今日目标
- 一个类实现多个接口
- 理解接口之间的继承
- 认识显式接口实现与为什么需要它

## 学习内容

### 多接口实现

现实中一个类可以"能做很多事"。比如 `Student` 既能 `IReadable`（读书），又能 `IRunnable`（跑步）：

```csharp
interface IReadable
{
    void Read();
}

interface IRunnable
{
    void Run();
}

class Student : IReadable, IRunnable
{
    public void Read() => Console.WriteLine("读书中...");
    public void Run() => Console.WriteLine("跑步中...");
}
```

```csharp
Student s = new Student();
IReadable r = s;        // 把它当 IReadable 用
r.Read();
IRunnable run = s;       // 也能当 IRunnable 用
run.Run();
```

### 接口继承

接口也可以继承接口，组合能力：

```csharp
interface IWorker
{
    void Work();
}

interface IManager : IWorker     // IManager 自动有 Work()
{
    void Meeting();
}

class Programmer : IManager
{
    public void Work() => Console.WriteLine("写代码");
    public void Meeting() => Console.WriteLine("开会");
}
```

### 显式接口实现

两个接口有同名方法时，怎么办？

```csharp
interface IEnglishSpeaker { void Speak(); }
interface IChineseSpeaker { void Speak(); }

class Person : IEnglishSpeaker, IChineseSpeaker
{
    // 显式实现：只能通过接口类型调用
    void IEnglishSpeaker.Speak() => Console.WriteLine("Hello!");
    void IChineseSpeaker.Speak() => Console.WriteLine("你好！");
}
```

```csharp
Person p = new Person();
// p.Speak();                          // 错：不知道调哪个
((IEnglishSpeaker)p).Speak();         // Hello!
((IChineseSpeaker)p).Speak();         // 你好！
```

### 面向接口解耦实战

写一个通知方法，不依赖具体短信/邮件类：

```csharp
interface INotifier
{
    void Send(string msg);
}

class EmailNotifier : INotifier
{
    public void Send(string msg) => Console.WriteLine($"发邮件：{msg}");
}

class SmsNotifier : INotifier
{
    public void Send(string msg) => Console.WriteLine($"发短信：{msg}");
}

class OrderService
{
    private readonly INotifier _notifier;
    public OrderService(INotifier notifier) { _notifier = notifier; }

    public void Checkout() => _notifier.Send("订单已下单");
}

// 想用邮件就传 EmailNotifier，想换短信就换 SmsNotifier，业务代码不动
OrderService svc = new OrderService(new EmailNotifier());
svc.Checkout();
```

这就是后面依赖注入（Day73）的雏形。

## 核心重点
- 一个类可实现多个接口，逗号分隔
- 接口可以继承接口，组合能力
- 同名接口方法用显式实现 `void IXxx.Method()`
- 面向接口编程：业务依赖接口，不依赖具体类
- 换实现时业务代码零修改

## 练习/任务
1. 让 `Student` 同时实现 IReadable、IRunnable、ISwimmable
2. 定义 `IStorage` 接口（Save/Load），做 `FileStorage` 与 `MemoryStorage` 两个实现
3. 写 `NewsService` 依赖 `IStorage`，分别用两种 Storage 跑一遍
4. （选做）故意做两个接口同名方法，用显式实现解决

## 打卡清单
- [ ] 会让一个类实现多个接口
- [ ] 会写接口继承接口
- [ ] 理解显式接口实现的用途
- [ ] 已体验面向接口替换实现
