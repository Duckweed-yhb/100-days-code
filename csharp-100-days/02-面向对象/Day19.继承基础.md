# Day 19 · 继承基础

## 今日目标
- 理解继承"is-a"关系，复用已有类
- 掌握 `:` 继承语法与 `base` 关键字
- 认识 protected 访问修饰符

## 学习内容

### 为什么需要继承

学生、老师都是"人"，都有姓名、年龄，都会介绍自己。把共性抽到 `Person`，再让 `Student`、`Teacher` 继承它，代码不重复。

```csharp
class Person
{
    public string Name { get; set; } = "";
    public int Age { get; set; }

    public void Introduce()
    {
        Console.WriteLine($"我叫 {Name}，{Age} 岁");
    }
}

// Student 继承 Person
class Student : Person
{
    public string School { get; set; } = "";     // 自己扩展的字段

    public void Study()
    {
        Console.WriteLine($"{Name} 在 {School} 学习");
    }
}
```

```csharp
Student s = new Student();
s.Name = "余泓彬";        // 继承来的
s.Age = 20;               // 继承来的
s.School = "哈工大深圳";   // 自己的
s.Introduce();            // 继承来的方法
s.Study();                // 自己的方法
```

### base 关键字

子类构造函数默认调父类无参构造。父类没有无参构造时，要显式 `: base(...)`：

```csharp
class Person
{
    public string Name { get; }
    public Person(string name) { Name = name; }
}

class Student : Person
{
    public string School { get; }
    public Student(string name, string school) : base(name)   // 调父类构造
    {
        School = school;
    }
}
```

`base.方法()` 也可以在子类里调父类已实现的方法。

### protected 修饰符

| 修饰符 | 谁能访问 |
|---|---|
| `public` | 任何人 |
| `private` | 只有自己类内部 |
| `protected` | 自己 + 派生类 |
| `internal` | 同一程序集内 |

父类字段想让子类用但对外藏起来，用 `protected`。

### 单继承

C# 类**只能继承一个父类**（不支持多类继承），但可以实现多个接口（后面讲）。所有类 implicitly 继承自 `object`，所以都有 `ToString()`、`Equals()` 等方法。

### 继承是"is-a"

- Student **is a** Person ✅
- 汽车继承轮子？❌ 那是"has-a"组合，别乱用继承。

## 核心重点
- 子类用 `class Student : Person` 继承父类
- 子类拿到父类 public/protected 成员
- `: base(...)` 调父类构造；`base.成员` 调父类成员
- `protected` 对子类开放、对外隐藏
- C# 单继承，多态靠接口实现

## 练习/任务
1. 把 Day14 的 Student、Teacher 统一改成继承 Person 基类
2. 给 Person 加 `protected string SSN`（身份证号），在 Student 里能访问，但外面不能
3. 写 `Animal` 基类（Name、Eat()），派生 `Dog`（Bark()）、`Cat`（Meow()）
4. （选做）让 Student 重写继承来的 Introduce，输出时多加一句学校（明天学 virtual）

## 打卡清单
- [ ] 会写 `class B : A` 继承
- [ ] 会用 base 调父类构造
- [ ] 理解 public/private/protected 区别
- [ ] 能区分 is-a 与 has-a
