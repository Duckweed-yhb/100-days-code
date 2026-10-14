# Day 95 · 单元测试编写

## 今日目标
- 给业务逻辑写单元测试
- 把业务逻辑从 handler 抽出来
- 用表驱动测试覆盖多场景

## 学习内容

### 把业务逻辑抽离

不要把数据库操作全写在 handler 里，抽成独立函数好测试：

```go
package todo

func Create(db *sql.DB, userID int, title string) (*Todo, error) {
    if title == "" {
        return nil, ErrEmptyTitle
    }
    res, err := db.Exec(`INSERT INTO todos(user_id, title) VALUES(?,?)`, userID, title)
    if err != nil {
        return nil, err
    }
    id, _ := res.LastInsertId()
    return &Todo{ID: int(id), Title: title, Done: false}, nil
}
```

### 表驱动测试

```go
func TestCreate(t *testing.T) {
    db := setupTestDB(t)
    cases := []struct {
        name    string
        title   string
        wantErr error
    }{
        {"正常", "买牛奶", nil},
        {"空标题", "", ErrEmptyTitle},
    }
    for _, c := range cases {
        t.Run(c.name, func(t *testing.T) {
            _, err := Create(db, 1, c.title)
            if err != c.wantErr {
                t.Errorf("err = %v, want %v", err, c.wantErr)
            }
        })
    }
}
```

### 测试数据库

用临时 SQLite 文件，每个测试独立。

## 核心重点
- 业务逻辑从 handler 抽离，便于测试
- 表驱动覆盖多场景
- 测试用独立临时数据库
- 先写可测试的代码，再测

## 练习/任务
1. 把 CreateTodo 业务逻辑抽到 todo 包
2. 写表驱动测试
3. 测试空标题报错
4. 跑 `go test -v ./...`

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
- [ ] 业务逻辑抽离
- [ ] 写好表驱动测试
- [ ] 测试通过
- [ ] 复习括号与链表
