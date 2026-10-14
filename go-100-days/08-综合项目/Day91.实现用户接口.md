# Day 91 · 实现用户接口

## 今日目标
- 实现注册接口
- 实现登录接口签发 JWT
- 实现鉴权中间件

## 学习内容

### 注册接口

```go
func Signup(c *gin.Context) {
    var req struct {
        Username string `json:"username" binding:"required,min=3"`
        Password string `json:"password" binding:"required,min=6"`
    }
    if err := c.ShouldBindJSON(&req); err != nil {
        c.JSON(400, gin.H{"error": err.Error()})
        return
    }
    res, err := db.Exec(`INSERT INTO users(username, password) VALUES(?,?)`,
        req.Username, req.Password)
    if err != nil {
        c.JSON(409, gin.H{"error": "用户名已存在"})
        return
    }
    id, _ := res.LastInsertId()
    c.JSON(200, gin.H{"id": id, "username": req.Username})
}
```

### 登录接口

```go
func Login(c *gin.Context) {
    var req struct {
        Username string `json:"username"`
        Password string `json:"password"`
    }
    c.ShouldBindJSON(&req)
    var id int
    err := db.QueryRow(`SELECT id FROM users WHERE username=? AND password=?`,
        req.Username, req.Password).Scan(&id)
    if err != nil {
        c.JSON(401, gin.H{"error": "用户名或密码错误"})
        return
    }
    token := jwt.NewWithClaims(jwt.SigningMethodHS256, jwt.MapClaims{
        "uid": id,
        "exp": time.Now().Add(24 * time.Hour).Unix(),
    })
    s, _ := token.SignedString(secret)
    c.JSON(200, gin.H{"token": s})
}
```

### 鉴权中间件

```go
func AuthRequired() gin.HandlerFunc {
    return func(c *gin.Context) {
        tokenStr := c.GetHeader("Authorization")
        token, err := jwt.Parse(tokenStr, func(t *jwt.Token) (any, error) {
            return secret, nil
        })
        if err != nil || !token.Valid {
            c.AbortWithStatusJSON(401, gin.H{"error": "未授权"})
            return
        }
        c.Set("uid", token.Claims.(jwt.MapClaims)["uid"])
        c.Next()
    }
}
```

## 核心重点
- 注册做参数校验与唯一约束
- 登录校验密码后签发 JWT
- 中间件验签并把 uid 存到 Context
- 密码明文仅示例，生产要 bcrypt

## 练习/任务
1. 实现注册与登录
2. 实现 AuthRequired 中间件
3. 用 curl 测试注册→登录
4. 故意传错密码看 401

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
- [ ] 注册接口可用
- [ ] 登录返回 JWT
- [ ] 鉴权中间件生效
- [ ] 复习括号与链表
