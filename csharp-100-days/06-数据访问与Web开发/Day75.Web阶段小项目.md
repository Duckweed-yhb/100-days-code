# Day 75 · Web阶段小项目

## 今日目标
- 综合 EF Core + ASP.NET Core + DI 做一个完整 Web API
- 跑通 CRUD + Swagger
- 复盘整个阶段

## 学习内容

### 项目：待办事项 API

需求：

1. 一个 Todo 实体（Id、Title、IsDone、CreatedAt）；
2. 完整 CRUD：列表、详情、创建、更新、删除；
3. 用 EF Core SQLite 存；
4. 用 Swagger 测试。

### 实体与 DbContext

```csharp
public class Todo
{
    public int Id { get; set; }
    public string Title { get; set; } = "";
    public bool IsDone { get; set; }
    public DateTime CreatedAt { get; set; } = DateTime.Now;
}

public class AppDbContext : DbContext
{
    public AppDbContext(DbContextOptions<AppDbContext> opt) : base(opt) { }
    public DbSet<Todo> Todos => Set<Todo>();
}
```

### Program.cs

```csharp
var builder = WebApplication.CreateBuilder(args);

builder.Services.AddControllers();
builder.Services.AddDbContext<AppDbContext>(opt =>
    opt.UseSqlite(builder.Configuration.GetConnectionString("Default")));
builder.Services.AddEndpointsApiExplorer();
builder.Services.AddSwaggerGen();

var app = builder.Build();

if (app.Environment.IsDevelopment())
{
    app.UseSwagger();
    app.UseSwaggerUI();
}

app.MapControllers();
app.Run();
```

### Controller

```csharp
[ApiController]
[Route("api/[controller]")]
public class TodosController : ControllerBase
{
    private readonly AppDbContext _db;
    public TodosController(AppDbContext db) { _db = db; }

    [HttpGet]
    public async Task<ActionResult<List<Todo>>> GetAll()
        => await _db.Todos.OrderByDescending(t => t.CreatedAt).ToListAsync();

    [HttpGet("{id}")]
    public async Task<ActionResult<Todo>> Get(int id)
    {
        var t = await _db.Todos.FindAsync(id);
        return t == null ? NotFound() : t;
    }

    [HttpPost]
    public async Task<ActionResult<Todo>> Create(Todo todo)
    {
        todo.Id = 0;
        _db.Todos.Add(todo);
        await _db.SaveChangesAsync();
        return CreatedAtAction(nameof(Get), new { id = todo.Id }, todo);
    }

    [HttpPut("{id}")]
    public async Task<IActionResult> Update(int id, Todo input)
    {
        var t = await _db.Todos.FindAsync(id);
        if (t == null) return NotFound();
        t.Title = input.Title;
        t.IsDone = input.IsDone;
        await _db.SaveChangesAsync();
        return NoContent();
    }

    [HttpDelete("{id}")]
    public async Task<IActionResult> Delete(int id)
    {
        var t = await _db.Todos.FindAsync(id);
        if (t == null) return NotFound();
        _db.Todos.Remove(t);
        await _db.SaveChangesAsync();
        return NoContent();
    }
}
```

### 跑起来

```powershell
dotnet ef migrations add Init
dotnet ef database update
dotnet run
```

访问 `/swagger` 测所有接口。

## 核心重点
- EF Core + Controller + DI 是 Web API 三件套
- AddDbContext 注册，Controller 构造注入
- MapControllers 注册路由
- Swagger 自动生成测试页
- 这就是真实后端服务的最小骨架

## 练习/任务
1. 实现上面的 Todo API 并跑通
2. 加一个 `GET /api/todos?done=true` 过滤已完成
3. 加一个 `PUT /api/todos/{id}/done` 只切换完成状态
4. （选做）给 Title 加 [Required] 校验

## 今日刷题
- **LeetCode 88 合并两个有序数组（复习）**：从后往前。
- **LeetCode 27 移除元素（复习）**：双指针。

## 打卡清单
- [ ] 跑通 Todo API
- [ ] 用 Swagger 测过所有接口
- [ ] 会用 EF Core 做 CRUD
- [ ] 已完成至少一道今日刷题
