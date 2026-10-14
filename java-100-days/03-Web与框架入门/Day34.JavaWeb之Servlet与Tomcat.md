# Day 34 · JavaWeb 之 Servlet 与 Tomcat

> 阶段：03-Web与框架入门 ｜ 重要程度：3.8 ｜ 源视频：https://www.bilibili.com/video/BV1m84y1w7Tb/

## 今日目标

- 跑通第一个 Servlet 程序，理解浏览器请求是如何被 Java 代码处理的
- 掌握 Tomcat 在 IDEA 中的部署与调试
- 理解 Servlet 的生命周期与请求 / 响应对象

## 学习内容

- **源路线**：JavaWeb 建议 3-4 天学完，重要程度 **3.8**
  - 视频：https://www.bilibili.com/video/BV1m84y1w7Tb/
  - 源文案重点：**Web 部分需要跟着敲下代码**；MySQL 直接全跳过；除了 Maven 高级部分基本都是重点，都可以跟着敲敲
- 今日进度：Tomcat 在 IDEA 中配置部署、第一个 Servlet、`@WebServlet` 注解、`HttpServletRequest` / `HttpServletResponse`、doGet / doPost
- 源文案提示：**Web 部分需要跟着敲下代码**，这部分不再是速览，要动手写

## 核心重点

- Tomcat 的作用：运行 Java Web 应用的容器
- IDEA 中配置 Tomcat（Application Server、Deployment、端口）
- Servlet 程序的编写：继承 `HttpServlet`、重写 `doGet` / `doPost`
- `@WebServlet("/xxx")` 配置访问路径
- `HttpServletRequest`：获取请求参数、请求头
- `HttpServletResponse`：设置响应类型、写出响应内容
- 请求转发与重定向的区别（初步认识）
- Servlet 生命周期：初始化、处理请求、销毁

## 练习/任务

1. 在 IDEA 中把 Maven Web 项目部署到 Tomcat，启动后浏览器能访问到页面
2. 写一个 Servlet：接收前端表单提交的用户名，在响应里返回「你好，xxx」
3. 分别测试 GET 和 POST 两种提交方式，观察参数获取
4. 故意改错路径或端口，学会看 Tomcat 控制台报错日志

## 今日刷题

- 打开力扣「hot100」题单（https://leetcode.cn/studyplan/）
- 今天刷「哈希」分类，挑 **1-2 道简单题**
- 策略：分类刷，简单 → 中等 → 困难；一道题 5 分钟没思路，直接看题解，从题解中举一反三；题目要反复刷

## 打卡清单

- [ ] Tomcat 在 IDEA 中部署成功，浏览器能访问
- [ ] 写出第一个能接收参数并响应的 Servlet
- [ ] 能说清 doGet / doPost 的区别与使用场景
- [ ] 今日刷题 1-2 道（hot100「哈希」简单题）
- [ ] 记录一个 Tomcat 部署报错及解决办法
