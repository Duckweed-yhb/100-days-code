# Day 20 · 多态与virtual

## 今日目标
- 理解多态：同一个调用，不同对象不同行为
- 掌握 `virtual` 与 `override`
- 学会用 `is` / `as` 做类型判断与转换

## 学习内容

### 没有 virtual 会怎样

```csharp
class Person
{
    public void Introduce() => Console.WriteLine("我是一个人");
}
class Student : Person
{
    public new void Introduce() => Console.WriteLine("我是学生");   // new 隐藏，不是多态
}

Person p = new Student();
p.Introduce();      // 输出"我是一个人"！不是学生版
```

这种"隐藏"不是真正的多态。要让运行时根据实际对象类型调用，用 `virtual`/`override`。

### virtual + override 实现多态

```csharp
class Person
{
    public virtual void Introduce() => Console.WriteLine("我是一个人");
}

class Student : Person
{
    public override void Introduce() => Console.WriteLine("我是学生");
}

class Teacher : Person
{
    public override void Introduce() => Console.WriteLine("我是老师");
}

Person[] people = { new Student(), new Teacher(), new Person() };
foreach (Person p in people)
{
    p.Introduce();     // 编译看 Person，运行看实际类型
}
// 输出：
// 我是学生
// 我是老师
// 我是一个人
```

这就是多态：**同一句 `p.Introduce()`，行为随对象变**。

### sealed 禁止再重写

```csharp
sealed override void Introduce() => ...;   // 子类不能再改
```

### 类型判断与转换

```csharp
Person p = new Student { School = "HIT" };

// is：判断是不是某类型
if (p is Student s)       // 顺便赋值给 s
{
    Console.WriteLine($"他在 {s.School}");
}

// as：安全转换，失败返回 null
Teacher? t = p as Teacher;
if (t == null) Console.WriteLine("不是老师");
```

### 多态的价值

写一段通用代码处理"一组 Person"，不用关心具体是 Student 还是 Teacher，加新子类时这段代码一行不改。这就是"对扩展开放、对修改关闭"。

## 核心重点
- 父类方法标 `virtual`，子类用 `override` 重写
- 多态：父类引用指向子类对象，调用时按实际类型分发
- `new` 是隐藏不是多态，慎用
- `is` 判断类型并模式匹配；`as` 安全转换
- `sealed` 阻止进一步重写

## 练习/任务
1. 让 Animal 基类的 `Speak()` 是 virtual，Dog/Cat 各自 override
2. 用 `Animal[]` 装不同动物，循环调用 Speak，看多态效果
3. 写方法 `void PrintInfo(Person p)`，内部用 is 判断并打印学生/老师专属信息
4. （选做）加一个 `Robot` 类也"继承"同一个 Speak 行为（提示：可能更适合用接口，明天想）

## 打卡清单
- [ ] 理解 virtual/override 机制
- [ ] 会写父类引用指向子类对象的多态调用
- [ ] 会用 is 做类型判断
- [ ] 分清 new 隐藏与 override 重写
