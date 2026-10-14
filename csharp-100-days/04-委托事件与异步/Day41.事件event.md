# Day 41 · 事件event

## 今日目标
- 理解事件的"发布-订阅"模式
- 学会用 `event` 关键字声明与触发事件
- 知道事件与委托的区别

## 学习内容

### 为什么需要事件

对象 A 状态变了，要通知 B、C、D……但 A 不认识它们。事件就是标准做法：A 发布事件，谁关心谁订阅。

### 声明事件

```csharp
class TemperatureSensor
{
    // 声明一个事件：用 EventHandler 委托
    public event EventHandler<TemperatureChangedEventArgs>? TemperatureChanged;

    private double _current;

    public void SetTemperature(double t)
    {
        if (Math.Abs(t - _current) < 0.1) return;
        _current = t;
        OnTemperatureChanged(t);     // 触发事件
    }

    protected virtual void OnTemperatureChanged(double t)
    {
        TemperatureChanged?.Invoke(this, new TemperatureChangedEventArgs(t));
    }
}

class TemperatureChangedEventArgs(double temp) : EventArgs
{
    public double Temperature { get; } = temp;
}
```

### 订阅事件

```csharp
TemperatureSensor sensor = new();

// 订阅：+=
sensor.TemperatureChanged += (sender, e) =>
    Console.WriteLine($"温度变了：{e.Temperature}°C");

sensor.TemperatureChanged += Alarm;   // 也可以挂命名方法

sensor.SetTemperature(25.0);    // 触发，两个订阅者都收到
sensor.SetTemperature(30.0);

static void Alarm(object? sender, TemperatureChangedEventArgs e)
{
    if (e.Temperature > 28) Console.WriteLine("⚠ 温度过高！");
}
```

### 事件 vs 普通委托字段

| 对比 | 委托字段 `public Action OnXxx` | 事件 `public event Action OnXxx` |
|---|---|---|
| 外部能否随意触发 | 能，`obj.OnXxx()` | 不能，只能 += / -= |
| 外部能否清空 | 能，`obj.OnXxx = null` | 不能 |

事件把"触发权"留在类内部，外部只能订阅/退订，这是封装。

### EventHandler 约定

.NET 约定：事件用 `EventHandler` 或 `EventHandler<TEventArgs>`，参数 sender + EventArgs。

## 核心重点
- 事件 = 发布-订阅，对象间松耦合通信
- `event` 关键字把委托字段包起来，外部只能 += / -=
- 触发事件用 `?.Invoke(sender, e)`
- 自定义事件参数继承 `EventArgs`
- UI 编程里按钮 Click 就是事件

## 练习/任务
1. 写一个 `Button` 类，含 `Click` 事件，模拟点击触发
2. 写 `Stock` 类，价格变动时触发 `PriceChanged` 事件，订阅者打印新价格
3. 用事件实现"观察者"：多个观察者订阅同一份数据变化
4. （选做）试试从外部直接调用 `sensor.TemperatureChanged(...)`，看编译报错

## 今日刷题
- **LeetCode 141 环形链表**：判断链表是否有环。思路：快慢指针，快的每次走两步，慢的走一步，相遇即有环。
- **LeetCode 142 环形链表 II**：找环入口。思路：快慢相遇后，一个指针回 head，两个都一步一步走，再次相遇即入口。

## 打卡清单
- [ ] 理解发布-订阅模式
- [ ] 会声明 event 并用 ?.Invoke 触发
- [ ] 会写自定义 EventArgs
- [ ] 已完成至少一道今日刷题
