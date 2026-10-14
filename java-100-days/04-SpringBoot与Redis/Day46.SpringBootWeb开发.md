# Day 46 · SpringBoot Web 开发

> 阶段：04-SpringBoot与Redis ｜ 重要程度：4.2 ｜ 源视频：https://www.bilibili.com/video/BV15b4y1a7yG/

## 今日目标

- 掌握 SpringBoot 接收请求、参数校验、返回 JSON 的完整流程
- 了解 SpringBoot Web 开发的常用注解与静态资源、拦截器等组件
- 能独立写出一个前后端分离风格的 CRUD 接口雏形

## 学习内容

- **源路线**：SpringBoot 建议 4-5 天学完，重要程度 **4.2**
  - 视频：https://www.bilibili.com/video/BV15b4y1a7yG/
  - 源文案定位：现在最主流的框架，但面试问的相对较少，但一定得会
- 今日进度：请求映射与参数接收、RESTful 风格接口、统一响应结果、参数校验、静态资源与拦截器、异常处理
- 源文案提示：**没啥重点非重点之分，基本上就是学如何用 SpringBoot 框架做配置，结合自己情况决定是否跟敲代码**

## 核心重点

- 请求映射：`@RequestMapping` / `@GetMapping` / `@PostMapping`
- 接收参数：`@RequestParam`、`@PathVariable`、`@RequestBody`
- 返回 JSON：`@RestController` 与自动序列化
- 统一响应结果（code / message / data）的封装思想
- 参数校验：`@Validated` 与常见校验注解
- 静态资源映射与 WebMvc 配置
- 拦截器（Interceptor）的基本作用与注册
- 全局异常处理：`@RestControllerAdvice` / `@ExceptionHandler`

## 练习/任务

1. 写一组用户 CRUD 接口（增删改查），用接口工具全部调通
2. 封装一个统一返回结果对象，所有接口都用它返回
3. 给某个参数加上校验，故意传错值观察全局异常处理的返回
4. 跟不跟敲结合自己情况：接口能跑通、报错能被统一处理就算达标

## 今日刷题

- 打开力扣「hot100」题单（https://leetcode.cn/studyplan/）
- 今天**挑战 1 道中等题**，不再只做简单题
- 策略：分类刷，简单 → 中等 → 困难；一道题 **5 分钟没思路直接看题解**，从题解中举一反三；看完遮住答案自己再写一遍，反复刷

## 打卡清单

- [ ] 写出并调通一组 CRUD 接口
- [ ] 完成统一返回结果封装
- [ ] 体验参数校验与全局异常处理
- [ ] 今日 hot100 挑战 1 道中等题（5 分钟没思路直接看题解）
- [ ] 记录一个接口联调时踩过的坑
