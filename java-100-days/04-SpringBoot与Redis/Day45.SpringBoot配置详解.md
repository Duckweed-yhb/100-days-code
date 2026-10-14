# Day 45 · SpringBoot 配置详解

> 阶段：04-SpringBoot与Redis ｜ 重要程度：4.2 ｜ 源视频：https://www.bilibili.com/video/BV15b4y1a7yG/

## 今日目标

- 掌握 SpringBoot 核心配置文件的写法与优先级
- 学会读取配置项、多环境配置的切换方式
- 理解「约定优于配置」，不再纠结配置细节

## 学习内容

- **源路线**：SpringBoot 建议 4-5 天学完，重要程度 **4.2**
  - 视频：https://www.bilibili.com/video/BV15b4y1a7yG/
  - 源文案定位：现在最主流的框架，但面试问的相对较少，但一定得会
- 今日进度：核心配置文件（application.properties / application.yml）、配置注入、多环境配置（profile）、配置文件加载顺序
- 源文案提示：**没啥重点非重点之分，基本上就是学如何用 SpringBoot 框架做配置，结合自己情况决定是否跟敲代码**

## 核心重点

- 核心配置文件：`application.properties` 与 `application.yml` 的写法区别
- 修改服务端口、应用名等常见配置项
- 配置注入：`@Value` 与 `@ConfigurationProperties` 读取配置
- 配置提示与松散绑定（relaxed binding）的概念
- 多环境配置：`application-dev` / `application-prod` 与 profile 切换
- 配置文件的加载位置与优先级约定
- 「约定优于配置」：绝大多数情况用默认值即可

## 练习/任务

1. 把上一个工程的端口和应用名写进 `application.yml`，重启验证生效
2. 用 `@Value` 注入一个自定义配置项并打印出来
3. 新建 dev / prod 两套 profile，分别切换启动观察差异
4. 跟不跟敲结合自己情况：能改配置、能读到值就算过关

## 今日刷题

- 打开力扣「hot100」题单（https://leetcode.cn/studyplan/）
- 今天做 **1-2 道简单题**，继续在熟悉的分类里巩固
- 策略：分类刷，简单 → 中等 → 困难；一道题 5 分钟没思路，直接看题解，从题解中举一反三；反复刷

## 打卡清单

- [ ] 会写 `application.yml` 并改端口、应用名
- [ ] 会用 `@Value` / `@ConfigurationProperties` 读到配置值
- [ ] 完成 dev / prod 多环境切换
- [ ] 今日 hot100 简单题 1-2 道
- [ ] 记录配置不生效时的排查思路
