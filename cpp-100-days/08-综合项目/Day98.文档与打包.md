# Day 98 · 文档与打包

## 今日目标
- 写一个 README 说明项目
- 整理目录结构
- 让别人能跑起来

## 学习内容

### README.md 应包含

```markdown
# TODO 命令行任务管理器

## 功能
- add/list/done/del/quit

## 编译
mkdir build && cd build
cmake ..
cmake --build .

## 使用
./todo
> add
> list
> quit

## 测试
./test_todo
```

### 目录整理

```
todo/
  README.md
  CMakeLists.txt
  include/
  src/
  tests/
  build/        # gitignore
  tasks.csv     # 运行时生成，gitignore
```

### .gitignore

```
build/
*.exe
tasks.csv
```

### 提交到 Git

```powershell
git init
git add .
git commit -m "todo v0.1"
```

### 用户手册

在 README 里列一个命令表：

| 命令 | 说明 |
|---|---|
| add | 添加任务 |
| list | 列出所有 |
| list prio | 按优先级 |
| list due | 按截止日期 |
| done \<id\> | 标记完成 |
| del \<id\> | 删除 |
| quit | 退出 |

## 核心重点
- README 让别人（和三个月后的你）能跑起来
- 目录干净，生成物 gitignore
- 命令列表写清楚
- 提交 git

## 练习/任务
1. 写 README.md
2. 加 .gitignore
3. git init 并提交
4. （选做）把项目推到 GitHub

## 今日刷题

- **LeetCode 56. 合并区间**。
- **LeetCode 57. 插入区间**。
- **LeetCode 252. 会议室**。

## 打卡清单
- [ ] 已写 README
- [ ] 已加 .gitignore
- [ ] 已 git 提交
- [ ] 文档里有命令表
