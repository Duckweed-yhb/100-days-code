# Day 36 · Git 实战一：本地仓库

> 阶段：03-Web与框架入门 ｜ 重要程度：4.3 ｜ 源视频：https://www.bilibili.com/video/BV1pX4y1S7Dq/

## 今日目标

- 装好 Git，配置好用户名邮箱，能用命令行操作
- 掌握本地仓库的完整流程：init / add / commit / log / 状态查看
- 理解工作区、暂存区、版本库三者的关系

## 学习内容

- **源路线**：Git 建议 1-2 天学完，重要程度 **4.3**，全世界都在用的代码管理工具，企业必用，但面试不问，因为已经默认你会这项技能了
  - 视频：https://www.bilibili.com/video/BV1pX4y1S7Dq/
  - 再结合菜鸟教程里的 Git 教程一起学习：https://www.runoob.com/git/git-tutorial.html
  - 源文案重点：**一定一定要动手实践，这是最需要你实践的一集**，本地，远程仓库如何搞，发生冲突如何解决等等这些，一定要实践
- 今日进度：Git 安装与配置、本地仓库初始化、文件的增删改与提交、查看历史版本、版本回退
- 源文案提示：**一定一定要动手实践**，光看视频不动手等于没学

## 核心重点

- Git 安装与 `git config --global user.name / user.email`
- `git init` 初始化本地仓库
- 工作区、暂存区（stage）、版本库（repository）的概念
- `git status` 查看当前文件状态
- `git add 文件名` / `git add .` 把改动放入暂存区
- `git commit -m "提交说明"` 提交到版本库
- `git log` / `git log --oneline` 查看提交历史
- `git reset --hard 版本号` 版本回退
- `.gitignore` 的作用与常见写法

## 练习/任务

1. 安装 Git，配置好全局用户名与邮箱，`git config --list` 能看到配置
2. 找一个空文件夹 `git init`，随便建几个 txt 文件，反复 add / commit 至少 5 次
3. 用 `git log --oneline` 看提交历史，再用 `git reset --hard` 故意回退到某一版，观察文件变化
4. 写一个 `.gitignore`，把编译产物（`target/`、`*.class`）忽略掉
5. **一定一定要动手实践**：以上每一条都要在自己机器上真敲一遍，不要只看视频

## 今日刷题

- 打开力扣「hot100」题单（https://leetcode.cn/studyplan/）
- 今天刷「栈」分类，挑 **1-2 道简单题**
- 策略：分类刷，简单 → 中等 → 困难；一道题 5 分钟没思路，直接看题解，从题解中举一反三；题目要反复刷

## 打卡清单

- [ ] Git 安装并配置好 user.name / user.email
- [ ] 完成至少 5 次 add / commit 循环
- [ ] 用 `git reset --hard` 成功回退过版本
- [ ] 写好 `.gitignore` 并生效
- [ ] 今日刷题 1-2 道（hot100「栈」简单题）
- [ ] 能讲清工作区 / 暂存区 / 版本库三者关系
