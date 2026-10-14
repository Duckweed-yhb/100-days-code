# Day 11 · 集合框架综合

> 阶段：01-JavaSE基础 ｜ 重要程度：5.0 ｜ 源视频：https://www.bilibili.com/video/BV17F411T7Ao/

## 今日目标

- 梳理完整集合体系，知道什么时候用哪个集合
- 掌握 `Collections` 工具类常用方法
- 理解并发修改异常产生原因与避免方式

## 学习内容

- **源路线**：JavaSE 建议 20-25 天学完，重要程度 **5.0**
  - 本阶段视频（见 Day01）：https://www.bilibili.com/video/BV17F411T7Ao/、https://www.bilibili.com/video/BV1yW4y1Y7Ms/
  - 「集合」部分综合巩固
- 今日进度：集合体系图、Collections 工具类、集合选型、迭代器中的删除问题
- 源文案提示：**代码稍微写点就行**，能选型、会用即可

## 核心重点

- 集合体系：Collection（List/Set）与 Map 两大分支
- List：ArrayList、LinkedList；Set：HashSet、TreeSet；Map：HashMap、LinkedHashMap
- `Collections` 工具类：`addAll()`、`shuffle()`、`sort()`、`reverse()`、`max/min`
- 集合选型：要索引用 List、去重用 Set、键值对用 Map
- 迭代器遍历过程中调用集合 `remove()` 会触发 `ConcurrentModificationException`
- 正确删除方式：用迭代器自己的 `remove()`，或增强 for 中不删
- 装箱拆箱与集合存基本类型的自动处理（了解）

## 练习/任务

1. 画一张集合体系结构图（Collection/Map 两大分支 + 常用实现类）
2. 用 `Collections.sort()` 对一个 List 排序
3. 在迭代器遍历中尝试删除元素，观察并发修改异常并修正
4. 用集合写一个小案例（如统计几个字符串出现次数）

## 打卡清单

- [ ] 能默画集合体系结构图
- [ ] 会用 Collections 工具类排序
- [ ] 理解并发修改异常原因
- [ ] 今天投入学习约 1.5 小时
- [ ] 记录今天遇到的报错与解决办法
