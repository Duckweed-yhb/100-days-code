# Day 71 · 控制器与路由

## 今日目标
- 从最小 API 过渡到 Controller 风格
- 掌握路由特性 `[Route]` `[HttpGet]` `[HttpPost]`
- 会做路径参数与查询参数绑定

## 学习内容

### 为什么需要 Controller

接口多了，全写在 Program.cs 里乱。Controller 把相关接口分组到类里。

```powershell
dotnet new webapi -o ApiDemo
cd ApiDemo
```

### 第一个 Controller

```csharp
[ApiController]
[Route("api/[controller]")]
public class StudentsController : ControllerBase
{
    private static readonly List<Student> _students = new()
    {
        new() { Id = 1, Name = "余泓彬", Score = 95 },
        new() { Id = 2, Name = "张三", Score = 55 },
    };

    // GET /api/students
    [HttpGet]
    public IEnumerable<Student> GetAll() => _students;

    // GET /api/students/1
    [HttpGet("{id:int}")]
    public ActionResult<Student> GetById(int id)
    {
        var s = _students.FirstOrDefault(x => x.Id == id);
        return s == null ? NotFound() : Ok(s);
    }

    // GET /api/students?minScore=60
    [HttpGet("search")]
    public IEnumerable<Student> Search([FromQuery] double minScore)
        => _students.Where(x => x.Score >= minScore);
}
```

### 路由特性

| 特性 | 绑定 |
|---|---|
| `[HttpGet]` | GET |
| `[HttpPost]` | POST |
| `[HttpPut("{id}")]` | PUT，路径参数 |
| `[HttpDelete("{id}")]` | DELETE |
| `[FromQuery]` | 从 query string 绑 |
| `[FromRoute]` | 从路径绑 |
| `[FromBody]` | 从请求体绑 JSON |

### 注册控制器

Program.cs：

```csharp
var builder = WebApplication.CreateBuilder(args);
builder.Services.AddControllers();
var app = builder.Build();
app.MapControllers();
app.Run();
```

## 核心重点
- Controller 继承 ControllerBase，加 [ApiController]
- [Route] 定义前缀，[HttpGet] 等定义动作
- `{id:int}` 路径参数带类型约束
- FromQuery/FromBody 显式绑定
- AddControllers + MapControllers 注册

## 练习/任务
1. 写 ProductsController，支持 GetAll/GetById
2. 加一个 POST 接口接收 JSON 新增产品
3. 加 PUT /api/products/{id} 更新价格
4. （选做）用 Swagger（默认 webapi 模板已带）测试接口

## 今日刷题
- **LeetCode 101 对称二叉树（复习）**：递归。
- **LeetCode 100 相同二叉树（复习）**：递归。

## 打卡清单
- [ ] 会写 Controller 与路由
- [ ] 会做路径参数与查询参数
- [ ] 知道 FromBody 绑 JSON
- [ ] 已完成至少一道今日刷题
