# Day 10 · 集合进阶之 Set 与 Map

> 阶段：01-JavaSE基础 ｜ 重要程度：5.0 ｜ 源视频：https://www.bilibili.com/video/BV17F411T7Ao/

## 今日目标

- 掌握 `Set` 接口特点与 `HashSet` 去重原理
- 掌握 `Map` 接口与 `HashMap` 的键值对操作
- 会遍历 Map 的两种方式

## 学习内容

- **源路线**：JavaSE 建议 20-25 天学完，重要程度 **5.0**
  - 本阶段视频（见 Day01）：https://www.bilibili.com/video/BV17F411T7Ao/、https://www.bilibili.com/video/BV1yW4y1Y7Ms/
  - 「集合」部分深入
- 今日进度：Set 接口、HashSet、TreeSet 了解、Map 接口、HashMap、Map 遍历
- 源文案提示：**代码稍微写点就行**，重点熟悉基本操作

## 核心重点

- Set 特点：不重复、无索引（不能普通 for 遍历）
- `HashSet`：底层哈希表，依赖 `hashCode()` 与 `equals()` 去重
- 存储自定义对象想去重，必须重写 hashCode 与 equals
- `TreeSet`：可排序（了解）
- Map 特点：键唯一、值可重复、键值对存储
- Map 常用方法：`put(k,v)`、`remove(key)`、`get(key)`、`containsKey()`、`keySet()`、`entrySet()`
- `HashMap`：最常用实现；`LinkedHashMap` 保证存取顺序（了解）
- Map 两种遍历：键找值（keySet）、键值对（entrySet）

## 练习/任务

1. 用 HashSet 存储字符串，观察去重效果
2. 自定义一个学生类，重写 hashCode/equals，验证 HashSet 去重
3. 用 HashMap 存几个键值对，用 keySet 与 entrySet 两种方式遍历
4. 整理一张「List vs Set vs Map」特点对比表

## 打卡清单

- [ ] 理解 Set 去重依赖 equals/hashCode
- [ ] 会用 HashMap 增删查
- [ ] 会用两种方式遍历 Map
- [ ] 今天投入学习约 1.5 小时
- [ ] 记录今天遇到的报错与解决办法
