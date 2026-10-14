# Day 99 · 打包发布与文档

## 今日目标
- dotnet publish 出产物
- 写一份 README 说清怎么跑
- 完成项目收尾

## 学习内容

### 发布

```powershell
dotnet publish src/Budget.Api -c Release -o publish
```

### README 该写什么

```markdown
# Budget API

个人记账 Web API。

## 运行
dotnet ef database update
dotnet run --project src/Budget.Api

## 接口
- GET /api/transactions?from=&to=
- POST /api/transactions
- GET /api/stats/total
- GET /api/stats/by-category

## 测试
dotnet test
```

### 检查清单

- [ ] dotnet build 通过
- [ ] dotnet test 全绿
- [ ] dotnet publish 出产物
- [ ] README 写好
- [ ] 数据库迁移脚本齐全

### 跑一次"陌生人视角"

按 README 一步步跑，看能不能起来。跑不起来就是文档有问题。

## 核心重点
- 发布前 build + test
- README 让别人能跑起来
- 文档是产品的一部分
- 迁移脚本要入库
- 项目收尾别草率

## 练习/任务
1. dotnet publish 出产物
2. 写 README
3. 按 README 从零跑一遍
4. （选做）写一个 CHANGELOG.md 记录这几天的改动

## 今日刷题
- **LeetCode 76 最小覆盖子串（复习）**：滑动窗口。
- **LeetCode 239 滑动窗口最大值（复习）**：单调队列。

## 打卡清单
- [ ] publish 成功
- [ ] README 写好
- [ ] 测试全绿
- [ ] 已完成至少一道今日刷题
