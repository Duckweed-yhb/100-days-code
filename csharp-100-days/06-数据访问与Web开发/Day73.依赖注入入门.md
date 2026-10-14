# Day 73 · 依赖注入入门

## 今日目标
- 理解依赖注入（DI）解决什么问题
- 会在 ASP.NET Core 注册服务并注入
- 区分三种生命周期

## 学习内容

### 紧耦合的问题

```csharp
class StudentsController
{
    private readonly StudentsRepository _repo = new();   // 自己 new，写死了
}
```

Controller 直接 new Repository，换实现（比如换成假的做测试）就得改 Controller。

### 依赖注入

把"创建依赖"的活交给框架：

```csharp
// 接口
public interface IStudentRepository
{
    List<Student> GetAll();
}

// 实现
public class StudentRepository : IStudentRepository
{
    public List<Student> GetAll() => ...;
}

// Controller 接收接口，不 new 实现
public class StudentsController : ControllerBase
{
    private readonly IStudentRepository _repo;
    public StudentsController(IStudentRepository repo) { _repo = repo; }
}
```

Program.cs 注册：

```csharp
builder.Services.AddScoped<IStudentRepository, StudentRepository>();
```

框架在造 Controller 时自动把 `StudentRepository` 塞进来。

### 三种生命周期

| 生命周期 | 说明 | 典型用途 |
|---|---|---|
| `AddTransient` | 每次都新建 | 轻量无状态服务 |
| `AddScoped` | 每个请求一个实例 | DbContext、业务服务 |
| `AddSingleton` | 整个应用一个实例 | 配置、缓存 |

### DbContext 注册

```csharp
builder.Services.AddDbContext<AppDbContext>(opt =>
    opt.UseSqlite("Data Source=app.db"));
```

然后 Controller 构造函数注入 `AppDbContext`。

### 为什么 DI 重要

- 解耦：Controller 依赖接口，不依赖具体实现；
- 可测试：测试时传假实现；
- 框架自动管生命周期与释放。

## 核心重点
- DI = 别自己 new，让框架塞进来
- 面向接口编程：构造函数收接口
- AddTransient/Scoped/Singleton 三种生命周期
- DbContext 默认 Scoped
- 这是 ASP.NET Core 的灵魂

## 练习/任务
1. 把 Controller 里的 List 数据抽到 IStudentRepository
2. 注册 Scoped 服务，Controller 构造函数注入
3. 把 DbContext 注册进 DI，Controller 注入使用
4. （选做）写一个假的 `FakeStudentRepository` 做单元测试

## 今日刷题
- **LeetCode 118 杨辉三角（复习）**：滚动数组。
- **LeetCode 119 杨辉三角 II（复习）**：从后往前覆盖。

## 打卡清单
- [ ] 理解 DI 解决紧耦合
- [ ] 会注册 Scoped 服务
- [ ] 会在构造函数注入
- [ ] 已完成至少一道今日刷题
