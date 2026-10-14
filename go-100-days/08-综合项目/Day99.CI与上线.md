# Day 99 · CI与上线

## 今日目标
- 理解 CI/CD 基本流程
- 写一个 GitHub Actions 工作流
- 知道上线前检查清单

## 学习内容

### CI/CD 是什么

- CI（持续集成）：每次 push 自动跑测试
- CD（持续部署）：测试通过自动发布

### GitHub Actions 工作流

`.github/workflows/test.yml`：

```yaml
name: Test
on: [push]
jobs:
  test:
    runs-on: ubuntu-latest
    steps:
      - uses: actions/checkout@v4
      - uses: actions/setup-go@v5
        with:
          go-version: "1.24"
      - run: go mod download
      - run: go test ./...
```

每次 push 自动跑测试，失败就红色叉。

### 上线前检查清单

- [ ] 所有测试通过
- [ ] 配置走环境变量
- [ ] 日志结构化
- [ ] 数据库有备份
- [ ] 有健康检查接口
- [ ] 优雅关闭

### 健康检查

```go
r.GET("/health", func(c *gin.Context) {
    c.JSON(200, gin.H{"status": "ok"})
})
```

## 核心重点
- CI 自动跑测试
- GitHub Actions 配置简单
- 配置外置、日志结构化、优雅关闭
- 健康检查给负载均衡用
- 上线前过检查清单

## 练习/任务
1. 写一个 GitHub Actions 工作流
2. 加 /health 接口
3. 列一份自己项目的上线检查清单
4. 把代码 push 到 GitHub 看 Actions 是否绿

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
- [ ] 写好 CI 工作流
- [ ] 健康检查接口可用
- [ ] 过完上线清单
- [ ] 复习括号与链表
