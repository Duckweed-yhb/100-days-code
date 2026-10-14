# Day 66 · EFCore入门

## 今日目标
- 理解 EF Core 是什么：对象关系映射（ORM）
- 装好 EF Core，建第一个 DbContext
- 用 C# 对象操作数据库，不写 SQL

## 学习内容

### ORM 是什么

ORM（Object-Relational Mapper）把数据库表映射成 C# 类：表 = 类，行 = 对象，列 = 属性。你写 C#，EF Core 帮你翻译 SQL。

### 安装

```powershell
dotnet new console -o EfDemo
cd EfDemo
dotnet add package Microsoft.EntityFrameworkCore.Sqlite
```

### 定义实体类与 DbContext

```csharp
using Microsoft.EntityFrameworkCore;

public class Student
{
    public int Id { get; set; }
    public string Name { get; set; } = "";
    public double Score { get; set; }
}

public class AppDbContext : DbContext
{
    public DbSet<Student> Students => Set<Student>();

    protected override void OnConfiguring(DbContextOptionsBuilder options)
    {
        options.UseSqlite("Data Source=efdemo.db");
    }
}
```

### 创建数据库与表

```csharp
using var db = new AppDbContext();
db.Database.EnsureCreated();    // 没有数据库就建一个
```

> `EnsureCreated` 适合 demo；生产用迁移（Day68）。

### 插入数据

```csharp
db.Students.Add(new Student { Name = "余泓彬", Score = 95 });
db.Students.Add(new Student { Name = "张三", Score = 55 });
await db.SaveChangesAsync();
```

### 查询

```csharp
List<Student> all = await db.Students.ToListAsync();
foreach (var s in all)
    Console.WriteLine($"{s.Id} {s.Name} {s.Score}");

var good = await db.Students.Where(s => s.Score >= 60).ToListAsync();
```

LINQ 表达式被翻译成 SQL，在数据库端筛选。

## 核心重点
- EF Core 是 ORM：表↔类，行↔对象
- DbContext 是工作单元，DbSet\<T> 是表
- UseSqlite/UseNpgsql 选数据库提供程序
- Add + SaveChanges 插入
- LINQ 自动翻译成 SQL

## 练习/任务
1. 装 EF Core，定义 Product 实体与 DbContext
2. 插入 3 个产品并 SaveChanges
3. 查询价格 > 10 的产品
4. （选做）对比 EnsureCreated 与 Migrations 的区别

## 今日刷题
- **LeetCode 121 买卖股票（复习）**：一次遍历。
- **LeetCode 122 买卖股票 II（复习）**：贪心。

## 打卡清单
- [ ] 理解 ORM 概念
- [ ] 会写 DbContext 与 DbSet
- [ ] 会用 Add + SaveChanges
- [ ] 已完成至少一道今日刷题
