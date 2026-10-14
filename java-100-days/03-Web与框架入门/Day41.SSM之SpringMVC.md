# Day 41 · SSM 之 SpringMVC

> 阶段：03-Web与框架入门 ｜ 重要程度：4.0 ｜ 源视频：https://www.bilibili.com/video/BV1JP4y1Z73S/

## 今日目标

- 理解 SpringMVC 在 Web 层做了什么，对比原生 Servlet 好在哪
- 掌握一个 Controller 接口的完整写法：接收请求、参数绑定、返回视图 / 数据
- 跑通第一个 SpringMVC Web 请求

## 学习内容

- **源路线**：SSM（Spring、SpringMVC、Mybatis）时间看个人情况，重要程度 **4.0**
  - 动力节点路线：https://www.bilibili.com/video/BV1JP4y1Z73S/
  - 源文案重点：**全篇重点，是否跟着敲代码看自己的掌握程度**
- 今日进度：SpringMVC 简介、核心架构（前端控制器 DispatcherServlet）、`@RequestMapping`、参数接收、视图解析、静态资源放行
- 源文案提示：**全篇重点**，是否跟着敲代码看自己的掌握程度

## 核心重点

- SpringMVC 是什么：Spring 提供的 Web 层框架，替代原生 Servlet
- 核心组件：DispatcherServlet（前端控制器）统一接管请求
- 一个请求在 SpringMVC 中的流转过程（浏览器 → DispatcherServlet → Controller → 视图 → 浏览器）
- `@Controller` 标记一个处理器类
- `@RequestMapping("/url")` 映射请求路径
- 方法参数自动绑定：直接用方法形参接收前端提交的参数
- 方法返回值：页面跳转 or 数据响应（`@ResponseBody` 初步认识）
- 与原生 Servlet 对比：不用再手动解析参数、不用手动写大量重复代码
- 视图解析器的作用

## 练习/任务

1. 新建一个 SpringMVC 项目，配置好 DispatcherServlet，启动后能访问到一个 Controller 方法
2. 写一个 Controller 方法：接收表单提交的用户名与年龄，跳转到一个结果页显示
3. 写一个方法用 `@ResponseBody` 直接返回一段字符串 / JSON（为前后端分离打基础）
4. 故意把请求路径写错，看 SpringMVC 报的 404 长什么样
5. 是否跟敲：**全篇重点，是否跟着敲代码看自己的掌握程度**

## 今日刷题

- 打开力扣「hot100」题单（https://leetcode.cn/studyplan/）
- 今天开始接触「动态规划」入门，挑 **1 道简单题**
- 策略：先看题解学思路，不要求独立做出来；分类刷，简单 → 中等 → 困难；一道题 5 分钟没思路，直接看题解，从题解中举一反三；题目要反复刷

## 打卡清单

- [ ] SpringMVC 项目跑通，浏览器能访问到 Controller 方法
- [ ] 写过一个带参数绑定的接口
- [ ] 写过一个 `@ResponseBody` 返回数据的方法
- [ ] 今日刷题 1 道（动态规划入门简单题，先看题解）
- [ ] 能用自己的话讲清 DispatcherServlet 是干什么的
