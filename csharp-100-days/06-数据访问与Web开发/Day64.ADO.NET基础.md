# Day 64 · ADO.NET基础

## 今日目标
- 理解 ADO.NET 是 .NET 最底层的数据库访问 API
- 认识 `SqlConnection`、`SqlCommand`、`SqlDataReader`
- 用纯 ADO.NET 连一次数据库

## 学习内容

### ADO.NET 是什么

ADO.NET 是 .NET 访问数据库的底层 API。后面学的 EF Core 底层也用它。它对应不同数据库有不同提供程序：

| 数据库 | NuGet 包 |
|---|---|
| SQL Server | `Microsoft.Data.SqlClient` |
| SQLite | `Microsoft.Data.Sqlite` |
| PostgreSQL | `Npgsql` |
| MySQL | `MySql.Data` / `Pomelo.EntityFrameworkCore.MySql` |

今天用 SQLite 演示，免装服务、文件即数据库。

### 安装包

```powershell
dotnet new console -o AdoDemo
cd AdoDemo
dotnet add package Microsoft.Data.Sqlite
```

### 连接数据库

```csharp
using Microsoft.Data.Sqlite;

// Data Source 指定数据库文件；不存在会自动创建
using var conn = new SqliteConnection("Data Source=demo.db");
conn.Open();
Console.WriteLine("已连接");
```

### 执行 SQL

```csharp
using var conn = new SqliteConnection("Data Source=demo.db");
conn.Open();

// 建表
using (var cmd = conn.CreateCommand())
{
    cmd.CommandText = """
        CREATE TABLE IF NOT EXISTS Students (
            Id INTEGER PRIMARY KEY AUTOINCREMENT,
            Name TEXT NOT NULL,
            Score REAL
        )
        """;
    cmd.ExecuteNonQuery();
}

// 插入
using (var cmd = conn.CreateCommand())
{
    cmd.CommandText = "INSERT INTO Students(Name, Score) VALUES (@name, @score)";
    cmd.Parameters.AddWithValue("@name", "余泓彬");
    cmd.Parameters.AddWithValue("@score", 95.5);
    cmd.ExecuteNonQuery();
}
```

> 永远用参数化 SQL（`@name`），不要字符串拼接，否则会 SQL 注入。

### 查询明天讲

今天先把连接与建表跑通。

## 核心重点
- ADO.NET 是底层 DB API，不同库有不同 Provider
- Sqlite 免安装，文件即数据库
- using 保证连接释放
- 参数化 SQL 防注入，绝不拼接
- ExecuteNonQuery 执行增删改

## 练习/任务
1. 装 Microsoft.Data.Sqlite，建一个 `Products` 表（Id、Name、Price）
2. 插入 3 条产品数据
3. 用参数化 SQL 插入，不要字符串拼接
4. （选做）查 SQL Server 与 SQLite 连接字符串写法区别

## 今日刷题
- **LeetCode 169 多数元素（复习）**：Boyer-Moore。
- **LeetCode 88 合并两个有序数组（复习）**：从后往前。

## 打卡清单
- [ ] 会用 ADO.NET 连接 SQLite
- [ ] 会建表与插入数据
- [ ] 会用参数化 SQL
- [ ] 已完成至少一道今日刷题
