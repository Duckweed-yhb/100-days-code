# Day 15 · 字段与构造函数

## 今日目标
- 学会用构造函数在创建对象时初始化数据
- 理解无参构造与有参构造
- 掌握 `this` 关键字与只读字段

## 学习内容

### 构造函数是什么

上一天我们 `new Student()` 后还得一行行 `s.Name = ...`。如果忘记赋值，字段就是默认值（null、0）。**构造函数**在 `new` 的时候自动执行，用来保证对象一诞生就是合法的。

```csharp
class Student
{
    public string Name;
    public int Age;

    // 构造函数：与类同名，无返回类型
    public Student(string name, int age)
    {
        Name = name;
        Age = age;
    }
}

// 创建时必须传参
Student s = new Student("余泓彬", 20);
Console.WriteLine(s.Name);   // 余泓彬
```

### 无参构造与默认构造

如果不写任何构造函数，编译器白送一个无参的。一旦写了有参构造，就**不再自动送**无参的了：

```csharp
class Student
{
    public string Name;
    public int Age;

    public Student() { Name = "未命名"; Age = 0; }     // 自己写无参

    public Student(string name, int age)
    {
        Name = name;
        Age = age;
    }
}

Student a = new Student();                  // 用无参
Student b = new Student("张三", 21);        // 用有参
```

### this 关键字

`this` 指"当前对象自己"。参数名与字段同名时用 `this` 区分：

```csharp
class Student
{
    public string Name;
    public int Age;

    public Student(string Name, int Age)   // 参数故意和字段同名
    {
        this.Name = Name;     // this.Name 是字段，Name 是参数
        this.Age = Age;
    }
}
```

### 构造函数重载与链式调用

一个类可以有多个构造函数，用 `: this(...)` 复用：

```csharp
class Student
{
    public string Name;
    public int Age;

    public Student(string name) : this(name, 18) { }   // 默认 18 岁

    public Student(string name, int age)
    {
        Name = name;
        Age = age;
    }
}
```

### readonly 字段

创建后不能再改的字段：

```csharp
class Product
{
    public readonly string Id;       // 只读
    public decimal Price;

    public Product(string id, decimal price)
    {
        Id = id;                     // 构造里能赋值
        Price = price;
    }
}

Product p = new Product("A001", 9.9m);
// p.Id = "B002";   // 错：readonly 不能在外面改
```

## 核心重点
- 构造函数与类同名、无返回类型，`new` 时自动调用
- 写了有参构造后，无参构造不再自动提供
- `this` 指当前对象，用于区分同名参数与字段
- `: this(...)` 让构造函数互相复用
- `readonly` 字段只能在声明或构造里赋值

## 练习/任务
1. 给上一天的 `Book` 类加有参构造，创建时直接传书名作者价格
2. 给 `Rectangle` 类加两个构造：一个默认 1×1，一个接收宽高
3. 写 `Point` 类：字段 x、y，加无参构造（原点）与有参构造，用 `: this()` 复用
4. （选做）把 Product 的 Id 改成 readonly，在外面试着改它，看编译报错

## 打卡清单
- [ ] 会写有参构造函数初始化对象
- [ ] 理解写了有参后无参要自己写
- [ ] 会用 this 区分字段与参数
- [ ] 会用 : this(...) 链式复用构造
