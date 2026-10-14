# Day 35 · JavaWeb 之 Filter 与会话管理

> 阶段：03-Web与框架入门 ｜ 重要程度：3.8 ｜ 源视频：https://www.bilibili.com/video/BV1m84y1w7Tb/

## 今日目标

- 掌握 Filter 的编写与拦截配置，理解「过滤链」思想
- 理解 Cookie 与 Session 的区别，完成一次登录态保持
- 收尾 JavaWeb 阶段，为后续 SSM 做铺垫

## 学习内容

- **源路线**：JavaWeb 建议 3-4 天学完，重要程度 **3.8**
  - 视频：https://www.bilibili.com/video/BV1m84y1w7Tb/
  - 源文案重点：**Web 部分需要跟着敲下代码**；MySQL 直接全跳过；除了 Maven 高级部分基本都是重点，都可以跟着敲敲
- 今日进度：Filter 过滤器编写与配置、统一字符编码、登录拦截；Cookie 与 Session 的概念、登录态保持、会话销毁
- 源文案提示：**Web 部分需要跟着敲下代码**；MySQL 部分全跳过，不花时间

## 核心重点

- Filter 的作用：在请求到达 Servlet 之前统一拦截处理
- 实现 `Filter` 接口，重写 `doFilter` 方法，配置拦截路径
- 过滤链：`chain.doFilter(request, response)` 放行请求
- 统一处理 POST 中文乱码：`request.setCharacterEncoding`
- Cookie：保存在浏览器端的小段数据
- Session：保存在服务器端的会话对象，靠 JSESSIONID 与浏览器关联
- 登录态保持：登录成功后把用户信息存入 Session
- Session 销毁与超时设置

## 练习/任务

1. 写一个 Filter，对所有请求统一设置 UTF-8 编码，解决 POST 中文乱码
2. 写一个登录拦截 Filter：未登录时访问受保护页面自动跳回登录页
3. 写一个最简登录流程：用户名密码提交 → 校验 → 写入 Session → 访问首页显示当前用户
4. 关闭浏览器再打开，观察 Session 是否还在（理解 Cookie / Session 关系）

## 今日刷题

- 打开力扣「hot100」题单（https://leetcode.cn/studyplan/）
- 今天刷「链表」分类，挑 **1-2 道简单题**
- 策略：分类刷，简单 → 中等 → 困难；一道题 5 分钟没思路，直接看题解，从题解中举一反三；题目要反复刷

## 打卡清单

- [ ] 写出一个统一编码的 Filter 并生效
- [ ] 写出登录拦截 Filter，未登录自动跳转
- [ ] 跑通「登录 → Session 保持 → 首页显示用户」流程
- [ ] 今日刷题 1-2 道（hot100「链表」简单题）
- [ ] 能用自己的话讲清 Cookie 与 Session 的区别
