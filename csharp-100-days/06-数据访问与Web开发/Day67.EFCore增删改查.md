# Day 67 · EFCore增删改查

## 今日目标
- 熟练 EF Core 的 CRUD
- 掌握 LINQ to Entities 常用查询
- 理解 SaveChanges 的工作时机

## 学习内容

### Create：插入

```csharp
db.Students.Add(new Student { Name = "李四", Score = 78 });
await db.SaveChangesAsync();
```

### Read：查询

```csharp
// 全部
var all = await db.Students.ToListAsync();

// 按主键
var s = await db.Students.FindAsync(1);

// 条件
var good = await db.Students
    .Where(x => x.Score >= 60)
    .OrderByDescending(x => x.Score)
    .ToListAsync();

// 投影成匿名类型
var names = await db.Students
    .Select(x => new { x.Name, x.Score })
    .ToListAsync();

// 单个
var first = await db.Students.FirstOrDefaultAsync(x => x.Name == "余泓彬");
```

### Update：修改

```csharp
var s = await db.Students.FindAsync(1);
if (s != null)
{
    s.Score = 100;
    await db.SaveChangesAsync();
}
```

取出对象 → 改属性 → SaveChanges，EF 自动生成 UPDATE。

### Delete：删除

```csharp
var s = await db.Students.FindAsync(2);
if (s != null)
{
    db.Students.Remove(s);
    await db.SaveChangesAsync();
}
```

### SaveChanges 时机

一个 DbContext 内多次增删改，最后一次 SaveChanges 统一提交。这叫**工作单元模式**：要么全成功，要么全回滚。

### 异步优先

查询与保存都用 `ToListAsync`、`FirstOrDefaultAsync`、`SaveChangesAsync`，别阻塞线程。

## 核心重点
- Add 插入、Remove 删除、改属性后 SaveChanges 更新
- FindAsync 按主键；Where + ToListAsync 条件查
- LINQ 翻译成 SQL，在数据库端筛选
- SaveChanges 统一提交
- 一律用异步版本

## 练习/任务
1. 给 Product 表做完整 CRUD
2. 查询价格前 3 的产品
3. 把所有 product 涨价 10%
4. （选做）用 CountAsync 统计产品数量

## 今日刷题
- **LeetCode 169 多数元素（复习）**：Boyer-Moore。
- **LeetCode 205 同构字符串（复习）**：Dictionary 双向映射。

## 打卡清单
- [ ] 会 EF Core 完整 CRUD
- [ ] 会用 LINQ 条件查询
- [ ] 理解 SaveChanges 工作单元
- [ ] 已完成至少一道今日刷题
