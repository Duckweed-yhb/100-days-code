# Day 15 · Lambda 与函数式接口

> 阶段：01-JavaSE基础 ｜ 重要程度：5.0 ｜ 源视频：https://www.bilibili.com/video/BV17F411T7Ao/

## 今日目标

- 理解 Lambda 表达式的作用与基本语法
- 掌握函数式接口的概念与常见内置接口
- 能把匿名内部类改写成 Lambda 写法

## 学习内容

- **源路线**：JavaSE 建议 20-25 天学完，重要程度 **5.0**
  - 本阶段视频（见 Day01）：https://www.bilibili.com/video/BV17F411T7Ao/、https://www.bilibili.com/video/BV1yW4y1Y7Ms/
  - 为后面 Stream 流打基础
- 今日进度：Lambda 体验、Lambda 标准格式、函数式接口、常用内置接口、方法引用（了解）
- 源文案提示：**代码稍微写点就行**，会读会写简单 Lambda 即可

## 核心重点

- Lambda 作用：简化匿名内部类（只适用于函数式接口）的写法
- 标准格式：`(参数) -> { 方法体 }`
- 函数式接口：有且仅有一个抽象方法的接口，配 `@FunctionalInterface`
- 省略规则：参数类型可推、单参数可省括号、单条语句可省 return 和花括号
- 常用内置函数式接口：Predicate（判断）、Consumer（消费）、Function（转换）、Supplier（供给）
- 方法引用 `::`（了解，后续项目多见）
- 不是所有匿名内部类都能改 Lambda，必须是函数式接口

## 练习/任务

1. 把一个 `Runnable` 匿名内部类改写成 Lambda
2. 用 Lambda 写一个 `Comparator`，对 List 排序
3. 分别用 Lambda 演示 Predicate、Consumer 各一次
4. 写一句注释：Lambda 适合用在什么场景

## 打卡清单

- [ ] 会写基本 Lambda 表达式
- [ ] 理解函数式接口
- [ ] 能把匿名内部类改成 Lambda
- [ ] 今天投入学习约 1.5 小时
- [ ] 记录今天遇到的报错与解决办法
