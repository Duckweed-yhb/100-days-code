# Day92 Git 与协作

> 目标：掌握团队协作里最实用的 Git 工作流——GitHub Flow、rebase vs merge、PR 礼仪、语义化版本，能看懂并写出规范的提交信息。

## 单机 Git 你早就会了

Day46 你学过 cargo，也用过 git init / add / commit。今天讲的是**多人协作**那部分：分支怎么用、代码怎么合回来、出冲突怎么办。

## GitHub Flow：最简单的分支模型

很多小团队和开源项目用这套，记住五步：

1. 从 `main` 拉一个 feature 分支
2. 在分支上提交
3. 推到远程，开 PR
4. Review + CI 通过后合并回 `main`
5. 删掉分支

就这么简单。不要搞复杂的 develop/release 分支模型——那是大公司的事，小项目 GitHub Flow 足够。

## rebase vs merge：到底用哪个

两条分支要合到一起，Git 给你两种方式：

| | `git merge` | `git rebase` |
|---|---|---|
| 历史记录 | 保留分叉，画出 merge 节点 | 把你的提交「挪」到目标分支顶，历史直线 |
| 冲突解决 | 一次解决 | 可能每个提交都要解决一次 |
| 风险 | 低，不改写历史 | 高，改写历史，已推送的别乱 rebase |

经验法则：

- **本地没推送的分支**：用 rebase，保持历史干净
- **已经推到共享分支**：用 merge，别 force push 坑队友
- 一句话：**rebase 让你提交更整齐，merge 更安全**

```powershell
# 把 main 的最新更新拉到你当前分支（变基）
git fetch origin
git rebase origin/main

# 或者安全地合并
git merge origin/main
```

## 常用命令速查

| 命令 | 作用 |
|---|---|
| `git status` | 看改了啥 |
| `git log --oneline -10` | 看最近 10 条提交 |
| `git diff` | 看未暂存的改动 |
| `git switch -c 分支名` | 新建并切到分支 |
| `git pull --rebase` | 拉远程并变基 |
| `git push -u origin 分支名` | 首次推送分支 |
| `git reset --hard` | 丢弃未提交改动（危险） |
| `git stash` | 临时藏起改动 |

## 提交信息规范

社区常用 **Conventional Commits**：用前缀说明这次改动类型：

```
feat: 新增短链生成接口
fix: 修复并发下缓存重复写入
docs: 更新安装说明
refactor: 重构用户模块，不改行为
test: 补充登录接口测试
chore: 升级依赖版本
```

好处：自动化工具能根据提交信息自动发版、生成 changelog。

## PR 描述与 Review 礼仪

- PR 标题写清意图，描述里贴截图/测试结果
- 小步提交，别一个 PR 改 2000 行
- Review 别人时：对事不对人，先夸再提建议
- 被 review 时：别玻璃心，提意见是帮你改好

## 语义化版本 SemVer

版本号 `MAJOR.MINOR.PATCH`（如 1.4.2）：

- **PATCH**（1.4.2→1.4.3）：修 bug，不破坏接口
- **MINOR**（1.4.2→1.5.0）：加新功能，向后兼容
- **MAJOR**（1.4.2→2.0.0）：不兼容的破坏性改动

发 crates.io 时遵守它，下游才敢升级。

## 今日小结

- 小团队用 GitHub Flow：feature 分支 → PR → 合并 main
- 本地用 rebase 保持整洁，共享分支用 merge，别 force push
- 提交信息用 Conventional Commits（feat/fix/docs…）
- PR 小而美，描述写清，CI 要绿
- 版本号遵守 SemVer：破坏才升 MAJOR

## 动手任务

1. 给自己的一个实验仓库建个 feature 分支，练一次 rebase
2. 把最近 5 条提交信息改成 Conventional Commits 风格
3. 模拟一次冲突：两人改同一行，练习怎么解决

**配合阅读**：[GitHub Flow 指南](https://docs.github.com/zh/get-started/quickstart/github-flow)
