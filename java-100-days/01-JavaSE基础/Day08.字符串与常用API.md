# Day 08 · 字符串与常用 API

> 阶段：01-JavaSE基础 ｜ 重要程度：5.0 ｜ 源视频：https://www.bilibili.com/video/BV17F411T7Ao/

## 今日目标

- 掌握 `String` 的不可变性与常用方法
- 理解 `==` 与 `equals` 的区别
- 会使用 `StringBuilder` 做字符串拼接与反转

## 学习内容

- **源路线**：JavaSE 建议 20-25 天学完，重要程度 **5.0**
  - 本阶段视频（见 Day01）：https://www.bilibili.com/video/BV17F411T7Ao/、https://www.bilibili.com/video/BV1yW4y1Y7Ms/
  - 源文案重点清单「字符串」今日攻克
- 今日进度：String 类、字符串内存原理、String 常用方法、StringBuilder
- 源文案提示：**代码稍微写点就行**，熟悉 API 调用即可

## 核心重点

- `String` 不可变，一旦创建内容不能改（底层是 final char[]/byte[]）
- 字符串常量池：`""` 直接赋值会复用常量池对象
- `==` 比较地址值，`equals` 比较内容
- 常用方法：`length()`、`charAt()`、`substring()`、`replace()`、`split()`、`trim()`、`equals()`
- 字符串拼接效率低，循环拼接用 `StringBuilder`
- `StringBuilder`：可变字符序列，`append()`、`reverse()`、`toString()`
- String 与 StringBuilder 相互转换

## 练习/任务

1. 用 `equals` 比较两个字符串内容，再用 `==` 比较，观察结果差异
2. 用常用方法完成：截取、替换、分割一段字符串
3. 用 StringBuilder 拼接 1-100 的字符串并反转
4. 整理 String 常用方法速查表

## 打卡清单

- [ ] 理解 String 不可变与常量池
- [ ] 会用 String 常用方法
- [ ] 会用 StringBuilder 做拼接
- [ ] 今天投入学习约 1.5 小时
- [ ] 记录今天遇到的报错与解决办法
