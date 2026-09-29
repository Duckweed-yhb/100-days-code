# Day82 数据库

> 目标：会用 Rust 连接真实数据库，理解 sqlx 编译期校验 SQL 的魅力，并能在 sqlx / diesel / SeaORM 之间做选择。

## Rust 连数据库有哪几条路

后端绕不开数据库。Rust 生态里连数据库主要分两派：

- **SQL 派**：你自己写 SQL，库帮你发请求、转类型。代表 **sqlx**
- **ORM 派**：用 Rust 结构体/方法代替写 SQL，库帮你生成 SQL。代表 **diesel**、**SeaORM**

大白话：SQL 派像「自己开车」，灵活但要懂 SQL；ORM 派像「坐导航」，省心但偶尔路线离谱。

| 维度 | sqlx | diesel | SeaORM |
|---|---|---|---|
| 风格 | 原生 SQL | 强类型 DSL（类 SQL 但用 Rust 写） | 实体 ORM，链式查询 |
| 异步 | 原生 async | 早期以同步为主 | 原生 async |
| 编译期校验 | `query!` 宏连库校验 | 编译期校验 | 运行期为主 |
| 学习曲线 | 低（会 SQL 就会） | 较陡 | 中等 |
| 适合 | 想掌控 SQL、用 Postgres/MySQL/SQLite | 喜欢强类型 DSL 的老项目 | 想要 async ORM 的团队 |

## sqlx：异步 + 编译期校验

sqlx 最大的卖点是 **`query!` 宏**：它在你**编译时**就连上数据库，把你的 SQL 发过去让数据库解析，检查列名对不对、类型匹不匹配。写错了？编译就报错，不用等到线上 3 点炸。

它支持 PostgreSQL、MySQL、SQLite、SQL Server。

### 配置依赖

```toml
[dependencies]
sqlx = { version = "0.8", features = ["runtime-tokio-rustls", "postgres", "macros"] }
tokio = { version = "1", features = ["full"] }
serde = { version = "1", features = ["derive"] }
```

### 建连接池并查询

```rust
use sqlx::postgres::PgPoolOptions;

// 一条用户记录，后面用 query_as! 直接映射上来
#[derive(Debug, sqlx::FromRow)]
struct User {
    id: i64,
    name: String,
}

#[tokio::main]
async fn main() -> Result<(), sqlx::Error> {
    // 1) 连接串，通常放环境变量
    let database_url = "postgres://user:pass@localhost:5432/demo";

    // 2) 建连接池（不要每次请求都新建连接）
    let pool = PgPoolOptions::new()
        .max_connections(5)
        .connect(database_url)
        .await?;

    // 3) 编译期校验的查询：? 是参数占位符
    //    编译时需要 DATABASE_URL 环境变量指向真库
    let row = sqlx::query!(
        "SELECT id, name FROM users WHERE id = $1",
        1_i64
    )
    .fetch_one(&pool)
    .await?;

    println!("用户：{} {}", row.id, row.name);

    // 4) 映射到结构体（也有编译期版本 query_as!）
    let users: Vec<User> = sqlx::query_as!(
        User,
        "SELECT id, name FROM users ORDER BY id LIMIT 10"
    )
    .fetch_all(&pool)
    .await?;
    println!("{:?}", users);

    Ok(())
}
```

### 两种查询方式别搞混

- `sqlx::query!` / `query_as!`（带感叹号）：**编译期校验**，需要 `DATABASE_URL`。CI 上没库怎么办？用 `cargo sqlx prepare` 把查询元数据存到 `.sqlx/` 目录，之后离线编译。
- `sqlx::query` / `query_as`（不带感叹号）：**运行期**发 SQL，不连库也能编译，灵活但没编译期保护。

### 插入一条数据

```rust
// 用参数绑定，彻底防 SQL 注入
let name = "浮萍";
let row = sqlx::query!(
    "INSERT INTO users (name) VALUES ($1) RETURNING id",
    name
)
.fetch_one(&pool)
.await?;
println!("新用户 id = {}", row.id);
```

记住：**永远用参数绑定（`$1`），不要字符串拼 SQL**，否则等于自己开门引 SQL 注入。

## diesel：强类型 ORM（了解）

diesel 不直接写 SQL 字符串，而是用一套 Rust DSL：

```rust
// 概念示意：不是真可编译代码，感受风格即可
// use schema::users;
// let results = users::table
//     .filter(users::name.eq("浮萍"))
//     .select((users::id, users::name))
//     .load::<(i32, String)>(&mut conn)?;
```

它在编译期把「类型不匹配的查询」挡下来，但配置较重、早期以同步为主。新项目如果想要 async ORM，很多人会转向 SeaORM。

## 选型建议

- 个人小项目 / 想写清爽 SQL：**sqlx**
- 需要 async ORM、不想写 SQL：**SeaORM**
- 老 diesel 项目维护：继续 diesel，不必折腾

## 今日小结

- sqlx 是异步 SQL 工具，`query!` 宏在编译期连库校验 SQL
- `DATABASE_URL` 是编译期校验的前提；CI 用 `cargo sqlx prepare` 离线模式
- 用连接池（`PgPool`），别每请求新建连接；用参数绑定防注入
- diesel 是强类型同步 ORM，SeaORM 是 async ORM
- 新项目默认 sqlx，要 ORM 再考虑 SeaORM

## 动手任务

1. 装一个本地 SQLite（零配置），用 sqlx 的 sqlite feature 跑通建表 + 插入 + 查询
2. 故意把 SQL 里的列名写错，观察 `query!` 宏在编译期报什么错
3. 跑一次 `cargo sqlx prepare`，看看 `.sqlx/` 目录里生成了什么

**配合阅读**：[sqlx 官方指南](https://sqlx.dev/)
