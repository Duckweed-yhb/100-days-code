# Day 18 · 异常处理

> 阶段：01-JavaSE基础 ｜ 重要程度：5.0 ｜ 源视频：https://www.bilibili.com/video/BV17F411T7Ao/

## 今日目标

- 理解异常继承体系（Error / Exception / RuntimeException）
- 掌握 `try-catch-finally` 与 `throws` / `throw`
- 会自定义简单异常类

## 学习内容

- **源路线**：JavaSE 建议 20-25 天学完，重要程度 **5.0**
  - 本阶段视频（见 Day01）：https://www.bilibili.com/video/BV17F411T7Ao/、https://www.bilibili.com/video/BV1yW4y1Y7Ms/
  - 源文案重点清单「异常」今日攻克
- 今日进度：异常体系、JVM 默认处理、try-catch、finally、throws/throw、自定义异常
- 源文案提示：**代码稍微写点就行**，重点是看懂异常堆栈

## 核心重点

- `Throwable` 顶层：`Error`（严重问题，不处理）与 `Exception`（程序异常）
- `Exception` 分两类：编译期受检异常（必须处理）与运行时异常 `RuntimeException`
- 常见运行时异常：空指针 NullPointerException、索引越界、类型转换异常
- 处理方式一：`try { } catch (异常 e) { } finally { }`
- `finally` 通常用于释放资源，一般会执行（除非 JVM 退出）
- 处理方式二：方法上 `throws` 抛出，交给调用者
- `throw new XxxException()` 手动抛出异常
- 自定义异常：继承 Exception 或 RuntimeException

## 练习/任务

1. 写一段会抛空指针的代码，用 try-catch 捕获并打印异常信息
2. 写一个带 finally 的例子，观察 finally 一定执行
3. 写一个方法用 `throws` 声明异常，在调用处处理
4. 自定义一个异常类（如 IllegalAgeException）并抛出

## 打卡清单

- [ ] 能说清异常继承体系
- [ ] 会用 try-catch-finally
- [ ] 理解 throws 与 throw 区别
- [ ] 今天投入学习约 1.5 小时
- [ ] 记录今天遇到的报错与解决办法
