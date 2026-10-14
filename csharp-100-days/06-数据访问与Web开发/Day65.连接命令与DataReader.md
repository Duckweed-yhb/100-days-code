# Day 65 · 连接命令与DataReader

## 今日目标
- 用 `SqlDataReader` 逐行读查询结果
- 学会 ExecuteScalar 取单个值
- 理解 ADO.NET 完整流程

## 学习内容

### 查询并遍历

```csharp
using var conn = new SqliteConnection("Data Source=demo.db");
conn.Open();

using var cmd = conn.CreateCommand();
cmd.CommandText = "SELECT Id, Name, Score FROM Students WHERE Score >= @min";
cmd.Parameters.AddWithValue("@min", 60);

using SqliteDataReader reader = cmd.ExecuteReader();

Console.WriteLine($"{"Id",-3} {"Name",-8} {"Score",-6}");
while (reader.Read())     // 逐行前进
{
    int id = reader.GetInt32(0);
    string name = reader.GetString(1);
    double score = reader.GetDouble(2);
    Console.WriteLine($"{id,-3} {name,-8} {score,-6:F1}");
}
```

### ExecuteScalar：取单个值

```csharp
using var cmd = conn.CreateCommand();
cmd.CommandText = "SELECT COUNT(*) FROM Students";
long count = (long)cmd.ExecuteScalar()!;
Console.WriteLine($"共 {count} 个学生");
```

### ExecuteNonQuery：增删改，返回影响行数

```csharp
using var cmd = conn.CreateCommand();
cmd.CommandText = "UPDATE Students SET Score = @s WHERE Id = @id";
cmd.Parameters.AddWithValue("@s", 100);
cmd.Parameters.AddWithValue("@id", 1);
int rows = cmd.ExecuteNonQuery();
Console.WriteLine($"更新了 {rows} 行");
```

### 完整流程图

```
打开连接
  → 创建 Command
  → 写 SQL + 参数
  → ExecuteNonQuery / ExecuteReader / ExecuteScalar
  → 读结果
  → using 自动释放
```

### 为什么 ADO.NET 这么啰嗦

这就是为什么有了 EF Core（明天）——把这些样板代码替你写了。但懂底层，排查问题才不慌。

## 核心重点
- DataReader.Read() 逐行前进
- GetInt32/GetString/GetDouble 按列号取
- ExecuteScalar 取第一行第一列
- ExecuteNonQuery 返回影响行数
- 所有对象都包 using

## 练习/任务
1. 查询 Students 表所有学生，按成绩降序打印
2. 用 ExecuteScalar 求平均分
3. 删除成绩低于 60 的学生，打印删除了几行
4. （选做）把读取结果映射成 List\<Student> 对象

## 今日刷题
- **LeetCode 274 H 指数（复习）**：排序后遍历。
- **LeetCode 380 O(1) 随机集合（复习）**：Dictionary + List。

## 打卡清单
- [ ] 会用 DataReader 逐行读
- [ ] 会用 ExecuteScalar 取单值
- [ ] 理解三种 Execute 的区别
- [ ] 已完成至少一道今日刷题
