# Day 72 · WebAPI返回数据

## 今日目标
- 掌握返回 JSON、状态码、问题详情
- 学会 POST/PUT 接收请求体
- 理解模型校验

## 学习内容

### 返回 JSON

Controller 方法返回对象，ASP.NET Core 自动序列化成 JSON：

```csharp
[HttpGet("{id}")]
public ActionResult<Student> Get(int id)
{
    var s = _students.FirstOrDefault(x => x.Id == id);
    if (s == null) return NotFound(new { message = "学生不存在" });
    return Ok(s);
}
```

### 常用返回类型

| 方法 | 状态码 | 含义 |
|---|---|---|
| `Ok(obj)` | 200 | 成功 |
| `Created($"/api/students/{id}", obj)` | 201 | 创建成功 |
| `NoContent()` | 204 | 成功无内容 |
| `BadRequest(...)` | 400 | 参数错 |
| `NotFound(...)` | 404 | 不存在 |
| `StatusCode(500, ...)` | 500 | 服务器错 |

### 接收 POST 请求体

```csharp
public class CreateStudentDto
{
    [Required]
    public string Name { get; set; } = "";

    [Range(0, 150)]
    public double Score { get; set; }
}

[HttpPost]
public IActionResult Create([FromBody] CreateStudentDto dto)
{
    var s = new Student { Id = new Random().Next(100, 999), Name = dto.Name, Score = dto.Score };
    _students.Add(s);
    return CreatedAtAction(nameof(Get), new { id = s.Id }, s);
}
```

`[ApiController]` 自动做模型校验：`[Required]` 不满足直接 400，不用手写 if。

### 状态码约定

- GET 查询 → 200
- POST 创建 → 201
- PUT/PATCH 更新 → 200 或 204
- DELETE → 204

### Swagger

`dotnet new webapi` 默认带 Swagger，运行后访问 `/swagger`，浏览器里直接测接口。

## 核心重点
- 返回对象自动序列化成 JSON
- Ok/Created/BadRequest/NotFound 选合适状态码
- [FromBody] 绑 JSON，[Required]/[Range] 自动校验
- POST 创建返回 201 + Location
- Swagger 是开发利器

## 练习/任务
1. 给 Products 加 POST 创建接口
2. 加 PUT 更新价格，产品不存在返回 404
3. 加 DELETE 删除接口，返回 204
4. （选做）故意 POST 缺 Name，看自动 400

## 今日刷题
- **LeetCode 112 路径总和（复习）**：递归。
- **LeetCode 113 路径总和 II（复习）**：DFS + 回溯。

## 打卡清单
- [ ] 会返回合适状态码
- [ ] 会用 [FromBody] 接收 JSON
- [ ] 会用数据注解做校验
- [ ] 已完成至少一道今日刷题
