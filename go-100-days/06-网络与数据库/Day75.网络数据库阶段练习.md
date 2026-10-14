# Day 75 · 网络数据库阶段练习

## 今日目标
- 综合 TCP/HTTP/数据库写一个小服务
- 把前面学的网络与数据库串起来
- 完成阶段六算法练习

## 学习内容

### 项目：一个极简备忘录 API

需求：用 SQLite 存备忘，HTTP 暴露 GET/POST 接口。

```go
package main

import (
    "database/sql"
    "encoding/json"
    "net/http"
    "strconv"

    _ "github.com/mattn/go-sqlite3"
)

type Note struct {
    ID   int    `json:"id"`
    Text string `json:"text"`
}

var db *sql.DB

func setupDB() {
    var err error
    db, err = sql.Open("sqlite3", "./notes.db")
    if err != nil {
        panic(err)
    }
    db.Exec(`CREATE TABLE IF NOT EXISTS notes (
        id INTEGER PRIMARY KEY AUTOINCREMENT,
        text TEXT
    )`)
}

func listNotes(w http.ResponseWriter, r *http.Request) {
    rows, err := db.Query(`SELECT id, text FROM notes`)
    if err != nil {
        http.Error(w, err.Error(), 500)
        return
    }
    defer rows.Close()

    var notes []Note
    for rows.Next() {
        var n Note
        rows.Scan(&n.ID, &n.Text)
        notes = append(notes, n)
    }
    json.NewEncoder(w).Encode(notes)
}

func addNote(w http.ResponseWriter, r *http.Request) {
    var n Note
    json.NewDecoder(r.Body).Decode(&n)
    res, _ := db.Exec(`INSERT INTO notes(text) VALUES(?)`, n.Text)
    id, _ := res.LastInsertId()
    n.ID = int(id)
    json.NewEncoder(w).Encode(n)
}

func main() {
    setupDB()
    http.HandleFunc("/notes", listNotes)
    http.HandleFunc("/notes/create", addNote)
    http.ListenAndServe(":8080", nil)
}
```

要点：HTTP 服务 + SQLite + JSON 编解码。这就是一个完整后端 API 的雏形。

## 核心重点
- HTTP handler 调数据库
- json.Encoder 直接写响应
- 错误用 http.Error 返回
- 一个完整 CRUD 服务就是这么简单

## 练习/任务
1. 跑通备忘录 API
2. 加一个删除接口 /notes/delete?id=N
3. 加错误处理：text 为空时返回 400
4. 用 curl 测试所有接口

## 今日刷题

### 1. LeetCode 146 LRU 缓存
- 思路：map + 双向链表，O(1) get/put。
- 代码提示：Go 标准库 list.List 可用。
- Go 要点：经典设计题。

### 2. LeetCode 200 岛屿数量（复习）
- 思路：DFS。
- 代码提示：沉没。
- Go 要点：网格。

## 打卡清单
- [ ] 跑通备忘录 API
- [ ] 加删除接口
- [ ] 写完 LRU 缓存
- [ ] 阶段六所有概念亲手写过
