# Day 68 · EFCore迁移与关系

## 今日目标
- 用 Migrations 管理数据库表结构变更
- 配置实体间关系（一对多）
- 理解导航属性

## 学习内容

### 为什么需要迁移

`EnsureCreated` 只在数据库不存在时建表。一旦改了实体类（加个字段），老数据库不会自动改。迁移（Migration）就是版本化地记录表结构变更。

### 安装迁移工具

```powershell
dotnet tool install --global dotnet-ef
dotnet add package Microsoft.EntityFrameworkCore.Design
```

### 创建并应用迁移

```powershell
dotnet ef migrations add Init
dotnet ef database update
```

之后改了实体类：

```powershell
dotnet ef migrations add AddPriceToProduct
dotnet ef database update
```

### 一对多关系

一个班级有多个学生：

```csharp
public class Class
{
    public int Id { get; set; }
    public string Name { get; set; } = "";
    public List<Student> Students { get; set; } = new();   // 导航属性
}

public class Student
{
    public int Id { get; set; }
    public string Name { get; set; } = "";
    public int ClassId { get; set; }                 // 外键
    public Class? Class { get; set; }                 // 导航属性
}
```

### 包含关联数据

```csharp
// 默认不连表，要 Include
var classes = await db.Classes
    .Include(c => c.Students)
    .ToListAsync();

foreach (var c in classes)
{
    Console.WriteLine($"{c.Name}：");
    foreach (var s in c.Students) Console.WriteLine($"  {s.Name}");
}
```

### 级联删除（了解）

删班级时默认级联删学生，用 `modelBuilder.Entity<Student>().HasOne(s=>s.Class).WithMany(c=>c.Students).OnDelete(DeleteBehavior.Restrict)` 改。

## 核心重点
- Migrations 版本化管表结构变更
- dotnet ef migrations add / database update
- 一对多：一方集合导航，多方外键 + 引用导航
- Include 才会连表查
- 实体改完记得加迁移

## 练习/任务
1. 给 Product 加 Category 字段，建迁移并 update
2. 定义 Author 与 Book（一对多），写迁移
3. 查询所有 Author 并 Include 他们的 Books
4. （选做）查 `HasMany` `WithOne` Fluent API 怎么配置关系

## 今日刷题
- **LeetCode 141 环形链表（复习）**：快慢指针。
- **LeetCode 142 环形链表 II（复习）**：找入口。

## 打卡清单
- [ ] 会建迁移并 update 数据库
- [ ] 会写一对多关系
- [ ] 会用 Include 连表
- [ ] 已完成至少一道今日刷题
