# Day91 开源贡献

> 目标：知道怎么给开源项目提第一份 PR，学会筛选 good first issue、走 fork→分支→PR→review 流程，并了解 Rust 的 RFC 机制。

## 为什么要给开源贡献

简历上写「参与过某开源项目」比写「精通 Rust」有说服力得多——它证明你能读别人的代码、按别人的规范协作。而且 Rust 社区非常友好，特别欢迎新手。

## 第一步：挑一个 good first issue

别一上来改核心逻辑。找带 **`good first issue`** 标签的任务，它们通常是：

- 修个文档错别字
- 加一个小测试
- 补一个简单功能

筛选技巧：

1. 去你用过的项目仓库，点 **Issues**，搜标签 `good first issue`
2. 挑最近 3 个月还活跃维护的项目（看 commit 时间）
3. 挑评论区维护者已经明确说「欢迎贡献」的
4. 看不懂的问题先在评论区问，别闷头写半天方向错了

## 标准协作流程：fork → 分支 → PR

```powershell
# 1) 在 GitHub 上点 Fork，把别人的仓库复制到自己账号下
# 2) 克隆你自己 fork 的版本
git clone https://github.com/你的用户名/项目.git
cd 项目

# 3) 加原仓库为上游，方便同步
git remote add upstream https://github.com/原作者/项目.git

# 4) 新建一个分支（别直接在 main 上改）
git checkout -b fix/typo-readme

# 5) 改代码、提交
git add .
git commit -m "docs: 修正 README 里的错别字"

# 6) 推到你自己的 fork
git push origin fix/typo-readme

# 7) 在 GitHub 网页上点 Compare & Pull Request，发 PR
```

## PR 描述怎么写

PR 不是只丢个链接。描述里说清：

- **改了什么**：一句话
- **为什么改**：关联 issue 编号（写 `Fixes #123`，合并时自动关 issue）
- **怎么验证**：跑了什么测试

## Review 礼仪

- 维护者让你改，客气地改，别顶嘴
- 一次 PR 只做一件事，别混一堆无关改动
- CI（自动测试）挂了，先自己修绿再求 review
- 耐心等——维护者都是 volunteer，可能几天才回

## Rust 特有的 RFC 流程

Rust 语言本身的重大改动要走 **RFC（Request For Comments）**：先在 `rust-lang/rfcs` 仓库写一份设计文档，社区讨论通过后才动手实现。想参与 Rust 语言设计，从读 RFC、评论 RFC 开始。

## 第一份 PR 行动清单

- [ ] 选一个你日常在用的 Rust 项目
- [ ] 找 `good first issue`，挑一个文档/小测试类
- [ ] 本地跑通测试套件（`cargo test`）
- [ ] fork → 分支 → 改 → 提 PR，描述写清楚
- [ ] 等 review，按意见修改直到合并

## 今日小结

- 从 `good first issue` 切入，别上来啃核心逻辑
- 流程：fork → 新分支 → 改 → PR → 按 review 修改 → 合并
- PR 要写清「改了什么、为什么、怎么验证」，关联 issue
- CI 要绿，一次 PR 只做一件事
- Rust 大改动走 RFC 流程（rust-lang/rfcs）

## 动手任务

1. 在 GitHub 找一个你用过的 Rust 库，列出 3 个 good first issue
2. 选其中一个文档类 issue，本地跑通 `cargo test`
3. （真动手）提你的第一份 PR，哪怕只是改个错别字

**配合阅读**：[RFC 仓库](https://github.com/rust-lang/rfcs)
