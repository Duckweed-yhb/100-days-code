# Day 72 · 预处理与事务

## 今日目标
- 理解预处理语句 Prepared Statement
- 学会用事务保证一致性
- 处理 SQL 错误

## 学习内容

### 预处理语句

`db.Exec` 每次都编译 SQL。反复执行同一语句时用预处理：

```go
stmt, err := db.Prepare(`INSERT INTO users(name, age) VALUES(?, ?)`)
if err != nil {
    log.Fatal(err)
}
defer stmt.Close()

for _, u := range users {
    stmt.Exec(u.Name, u.Age)
}
```

性能更好，也更安全。

### 事务

多条 SQL 要么全成功要么全回滚：

```go
tx, err := db.Begin()
if err != nil {
    log.Fatal(err)
}

// 任一操作失败就 Rollback
_, err = tx.Exec(`UPDATE accounts SET balance = balance - 100 WHERE id = 1`)
if err != nil {
    tx.Rollback()
    return err
}
_, err = tx.Exec(`UPDATE accounts SET balance = balance + 100 WHERE id = 2`)
if err != nil {
    tx.Rollback()
    return err
}

err = tx.Commit()   // 全部成功才提交
```

### 错误处理

- Exec 返回 sql.Result
- Query 后必须 rows.Close
- sql.ErrNoRows 是"没查到"，不是真错误

### 常见错误

- 占位符写错（MySQL 用 ?，Postgres 用 $1）
- 忘 Close rows 导致连接泄漏
- 事务忘了 Commit 或 Rollback

## 核心重点
- Prepare 复用 SQL，性能更好
- Begin 开事务，Commit 提交，Rollback 回滚
- 事务里任一失败就回滚
- sql.ErrNoRows 要单独处理
- rows/stmt 都要 Close

## 练习/任务
1. 用 Prepare 批量插入 10 个用户
2. 写一个转账事务：A 减 100、B 加 100
3. 故意让事务第二步失败，验证回滚
4. 查询一个不存在的 id，处理 sql.ErrNoRows

## 今日刷题

### 1. LeetCode 1 两数之和（复习）
- 思路：map。
- 代码提示：查 target-x。
- Go 要点：map。

### 2. LeetCode 88 合并两个有序数组（复习）
- 思路：从后往前。
- 代码提示：三指针。
- Go 要点：数组。

## 打卡清单
- [ ] 会用 Prepare 预处理
- [ ] 会写事务
- [ ] 会处理 ErrNoRows
- [ ] 复习两数之和
