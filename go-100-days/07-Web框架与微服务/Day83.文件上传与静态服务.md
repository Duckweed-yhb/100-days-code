# Day 83 · 文件上传与静态服务

## 今日目标
- 学会接收文件上传
- 学会托管静态文件
- 理解 multipart/form-data

## 学习内容

### 文件上传接口

```go
r.POST("/upload", func(c *gin.Context) {
    file, err := c.FormFile("file")
    if err != nil {
        c.JSON(400, gin.H{"error": err.Error()})
        return
    }
    c.SaveUploadedFile(file, "./uploads/"+file.Filename)
    c.JSON(200, gin.H{"filename": file.Filename, "size": file.Size})
})
```

### 静态文件服务

```go
r.Static("/uploads", "./uploads")
```

访问 http://localhost:8080/uploads/xx.png 就能拿到上传的文件。

### 限制上传大小

```go
r.MaxMultipartMemory = 8 << 20   // 8MB
```

### multipart 表单

前端用 `<form enctype="multipart/form-data">` 上传。

### 安全注意

- 不要信任用户文件名，防止路径穿越
- 限制上传大小
- 上传目录不要有执行权限

## 核心重点
- c.FormFile 接收上传文件
- SaveUploadedFile 保存
- r.Static 托管静态目录
- MaxMultipartMemory 限大小
- 上传文件名要消毒

## 练习/任务
1. 写一个图片上传接口
2. 上传后通过静态目录访问
3. 限制上传大小 1MB
4. 故意传一个 .exe 文件，思考如何过滤

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
- [ ] 会接收文件上传
- [ ] 会托管静态文件
- [ ] 会限制上传大小
- [ ] 复习括号与链表
