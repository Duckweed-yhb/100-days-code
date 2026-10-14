# Day 82 · 会话与JWT鉴权

## 今日目标
- 理解 Session 与 JWT 的区别
- 学会用 JWT 做无状态鉴权
- 在 gin 里保护路由

## 学习内容

### Session vs JWT

| 方案 | 状态 | 存储 |
|---|---|---|
| Session | 有状态 | 服务端存 |
| JWT | 无状态 | 客户端存 token |

JWT 把用户信息加密签名，服务端验签即可，不用存 session。

### 安装 jwt 库

```powershell
go get github.com/golang-jwt/jwt/v5
```

### 签发 token

```go
import "github.com/golang-jwt/jwt/v5"

secret := []byte("我的密钥")

func login(c *gin.Context) {
    token := jwt.NewWithClaims(jwt.SigningMethodHS256, jwt.MapClaims{
        "userID": 1,
        "exp":    time.Now().Add(24 * time.Hour).Unix(),
    })
    tokenString, _ := token.SignedString(secret)
    c.JSON(200, gin.H{"token": tokenString})
}
```

### 校验 token 中间件

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
        c.Next()
    }
}
```

### 使用

```go
r.POST("/login", login)
auth := r.Group("/", AuthRequired())
{
    auth.GET("/profile", profile)
}
```

## 核心重点
- JWT 无状态，服务端不存 session
- 登录成功签发 token，后续请求带在 Header
- 中间件验签
- 密钥要保密
- exp 设过期时间

## 练习/任务
1. 写 /login 接口返回 JWT
2. 写 AuthRequired 中间件保护 /profile
3. 用 Postman/curl 测试：不带 token 401，带 token 200
4. 故意过期 token 看拒绝

## 今日刷题

### 1. LeetCode 146 LRU 缓存（复习）
- 思路：map+链表。
- 代码提示：list.List。
- Go 要点：设计。

### 2. LeetCode 200 岛屿数量（复习）
- 思路：DFS。
- 代码提示：沉没。
- Go 要点：网格。

## 打卡清单
- [ ] 理解 JWT 原理
- [ ] 会签发与校验 token
- [ ] 会写鉴权中间件
- [ ] 复习 LRU 与岛屿
