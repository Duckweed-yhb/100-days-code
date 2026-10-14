# Day 71 · SQL增删改查

## 今日目标
- 学会 Exec 执行增删改
- 学会 Query/QueryRow 查询
- 正确扫描结果到结构体

## 学习内容

### 建表

```go
db.Exec(`CREATE TABLE IF NOT EXISTS users (
    id INTEGER PRIMARY KEY AUTOINCREMENT,
    name TEXT,
    age INTEGER
)`)
```

### 插入

```go
result, err := db.Exec(`INSERT INTO users(name, age) VALUES(?, ?)`, "Bob", 20)
id, _ := result.LastInsertId()
```

`?` 是占位符，防止 SQL 注入。

### 查询单行

```go
var name string
var age int
err := db.QueryRow(`SELECT name, age FROM users WHERE id = ?`, 1).Scan(&name, &age)
if err == sql.ErrNoRows {
    fmt.Println("没找到")
}
```

### 查询多行

```go
rows, err := db.Query(`SELECT id, name, age FROM users`)
if err != nil {
    log.Fatal(err)
}
defer rows.Close()

for rows.Next() {
    var id int
    var name string
    var age int
    rows.Scan(&id, &name, &age)
    fmt.Println(id, name, age)
}
```

### 更新与删除

```go
res, _ := db.Exec(`UPDATE users SET age = ? WHERE id = ?`, 21, 1)
res, _ = db.Exec(`DELETE FROM users WHERE id = ?`, 1)
```

## 核心重点
- Exec 执行增删改
- QueryRow 查单行，Query 查多行
- Scan 扫描到变量指针
- 占位符 ? 防注入
- rows 必须 Close

## 练习/任务
1. 建一个 users 表
2. 插入 3 条记录
3. 查询所有用户并打印
4. 更新一个用户年龄再查询验证

## 今日刷题

### 1. LeetCode 20 有效的括号（复习）
- 思路：栈。
- 代码提示：map。
- Go 要点：栈。

### 2. LeetCode 21 合并两个有序链表（复习）
- 思路：哑节点。
- 代码提示：谁小接谁。
- Go 要点：链表。

## 打卡清单
- [ ] 会写增删改查
- [ ] 会用 QueryRow/Query
- [ ] 理解占位符防注入
- [ ] 复习括号与链表
