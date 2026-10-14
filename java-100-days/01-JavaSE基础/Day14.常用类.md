# Day 14 · 常用类

> 阶段：01-JavaSE基础 ｜ 重要程度：5.0 ｜ 源视频：https://www.bilibili.com/video/BV17F411T7Ao/

## 今日目标

- 掌握包装类与自动装箱/拆箱
- 会使用 `Math`、`System`、`Arrays` 工具类
- 了解日期时间类的基本用法（点到为止）

## 学习内容

- **源路线**：JavaSE 建议 20-25 天学完，重要程度 **5.0**
  - 本阶段视频（见 Day01）：https://www.bilibili.com/video/BV17F411T7Ao/、https://www.bilibili.com/video/BV1yW4y1Y7Ms/
  - 常用 JDK 自带类的查漏补缺
- 今日进度：包装类、Math、System、Arrays、基本时间日期类
- 源文案提示：**代码稍微写点就行**，知道有哪些类、会查 API 即可

## 核心重点

- 八种基本类型对应包装类：Integer、Long、Double、Character 等
- 自动装箱（基本类型 → 包装类）与自动拆箱（反向）
- `Integer` 常用：`parseInt()` 字符串转整数
- `Math`：`abs()`、`max/min()`、`round()`、`random()`
- `System`：`currentTimeMillis()`、`arraycopy()`（了解）
- `Arrays`：`toString()`、`sort()`、`binarySearch()`、`asList()`
- 日期时间类 `Date`、`SimpleDateFormat`、`Calendar`（了解，后续会有更现代的时间 API）

## 练习/任务

1. 写几个自动装箱/拆箱的例子，如 `Integer i = 10;`
2. 用 `Integer.parseInt()` 把字符串 `"123"` 转成 int
3. 用 `Arrays.sort()` 对一个数组排序，再用 `Arrays.toString()` 打印
4. 用 `Math.random()` 生成几个 0-1 的随机数

## 打卡清单

- [ ] 理解自动装箱/拆箱
- [ ] 会用 Math / Arrays 常用方法
- [ ] 会用 parseInt 做字符串转数字
- [ ] 今天投入学习约 1.5 小时
- [ ] 记录今天遇到的报错与解决办法
