# Day 70 · databaseSql连接

## 今日目标
- 理解 database/sql 是 Go 数据库抽象层
- 学会打开连接池、Ping
- 认识驱动注册机制

## 学习内容

### database/sql 架构

Go 不内置数据库驱动，而是定义统一接口，各数据库提供驱动：

```
你的代码 → database/sql（标准接口）→ 驱动 → MySQL/Postgres/SQLite
```

### 打开数据库

以 SQLite 为例（无需装服务器，便于学习）：

```powershell
go get github.com/mattn/go-sqlite3
```

```go
import (
    "database/sql"
    _ "github.com/mattn/go-sqlite3"   // 只注册驱动，不直接用
)

db, err := sql.Open("sqlite3", "./test.db")
if err != nil {
    log.Fatal(err)
}
defer db.Close()
```

### 下划线导入

`_ "驱动包"` 表示只调用驱动的 init() 注册自己，不直接引用包里的符号。

### Ping 验证连接

`sql.Open` 并不真正建立连接，只是创建连接池。要 Ping：

```go
if err := db.Ping(); err != nil {
    log.Fatal("连接失败:", err)
}
```

### 连接池配置

```go
db.SetMaxOpenConns(10)    // 最大打开连接
db.SetMaxIdleConns(5)     // 最大空闲连接
```

## 核心重点
- database/sql 是标准抽象，驱动单独引入
- `_ "驱动"` 下划线导入注册
- sql.Open 不立即连接，Ping 才验证
- db.Close 释放连接池
- 生产环境配连接池参数

## 练习/任务
1. 用 SQLite 打开一个 test.db
2. Ping 验证连接成功
3. 配置连接池参数
4. 故意写错驱动名，看报错

## 今日刷题

### 1. LeetCode 141 环形链表（复习）
- 思路：快慢指针。
- 代码提示：相遇。
- Go 要点：链表。

### 2. LeetCode 104 二叉树最大深度（复习）
- 思路：递归。
- 代码提示：1+max。
- Go 要点：树。

## 打卡清单
- [ ] 会打开数据库连接
- [ ] 理解驱动注册机制
- [ ] 会 Ping 验证
- [ ] 复习链表与树
