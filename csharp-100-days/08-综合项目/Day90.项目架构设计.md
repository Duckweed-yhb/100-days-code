# Day 90 · 项目架构设计

## 今日目标
- 确定项目分层
- 划清各项目职责
- 把依赖关系理清楚

## 学习内容

### 分层架构

```
Budget.Api          ── 依赖 ──► Budget.Core ◄── 依赖 ── Budget.Infrastructure
   ▲                                  ▲
   └──────────────────────────────────┘
              (Api 也依赖 Infrastructure)
Budget.Tests 依赖 Core
```

| 项目 | 职责 |
|---|---|
| Budget.Core | 实体、接口、业务服务 |
| Budget.Infrastructure | EF Core、DbContext、具体仓储实现 |
| Budget.Api | Controllers、Program.cs |
| Budget.Tests | 单元测试 |

### 依赖规则

- Core **不依赖**任何其他项目；
- Infrastructure 依赖 Core；
- Api 依赖 Core 与 Infrastructure；
- Tests 依赖 Core。

这叫**依赖倒置**：高层不依赖低层，都依赖抽象。

### 今天的工作

1. `dotnet new sln` 建解决方案；
2. 建四个项目并加进解决方案；
3. 加项目引用。

```powershell
dotnet new sln -o Budget
cd Budget
dotnet new classlib -o src/Budget.Core
dotnet new classlib -o src/Budget.Infrastructure
dotnet new web -o src/Budget.Api
dotnet new xunit -o tests/Budget.Tests

dotnet sln add src/Budget.Core
dotnet sln add src/Budget.Infrastructure
dotnet sln add src/Budget.Api
dotnet sln add tests/Budget.Tests

dotnet add src/Budget.Infrastructure reference src/Budget.Core
dotnet add src/Budget.Api reference src/Budget.Core src/Budget.Infrastructure
dotnet add tests/Budget.Tests reference src/Budget.Core
```

### 为什么分层

- 换数据库？只改 Infrastructure；
- 换 Web 框架？只改 Api；
- Core 业务逻辑稳定。

## 核心重点
- Core / Infrastructure / Api / Tests 四层
- Core 不依赖别人
- 依赖抽象，不依赖实现
- 今天先搭骨架，明天填实体
- 架构清晰，后期才不烂尾

## 练习/任务
1. 按上面命令建解决方案与四个项目
2. 加好项目引用
3. `dotnet build` 确认能编过
4. （选做）把默认 Class1.cs 删掉

## 今日刷题
- **LeetCode 217 存在重复元素（复习）**：HashSet。
- **LeetCode 53 最大子数组和**：Kadane，dp[i]=max(nums[i], dp[i-1]+nums[i])。

## 打卡清单
- [ ] 解决方案建好了
- [ ] 四个项目依赖正确
- [ ] dotnet build 通过
- [ ] 已完成至少一道今日刷题
