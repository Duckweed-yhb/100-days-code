# Day 93 · 查询与排序功能

## 今日目标
- 实现按优先级筛选
- 实现按完成状态筛选
- 用 STL sort 排序

## 学习内容

### 在 Store 里加查询

```cpp
std::vector<Task> TaskStore::filterByPriority(int minPri) const {
    std::vector<Task> out;
    std::copy_if(tasks_.begin(), tasks_.end(), std::back_inserter(out),
        [minPri](const Task& t) { return t.priority <= minPri; });
    return out;
}

std::vector<Task> TaskStore::filterByDone(bool done) const {
    std::vector<Task> out;
    std::copy_if(tasks_.begin(), tasks_.end(), std::back_inserter(out),
        [done](const Task& t) { return t.done == done; });
    return out;
}
```

### 排序

```cpp
void TaskStore::sortByPriority() {
    std::sort(tasks_.begin(), tasks_.end(),
        [](const Task& a, const Task& b) { return a.priority < b.priority; });
}

void TaskStore::sortByDue() {
    std::sort(tasks_.begin(), tasks_.end(),
        [](const Task& a, const Task& b) { return a.due < b.due; });
}
```

### 命令扩展

```
list            # 全部
list prio       # 按优先级
list due        # 按截止日期
list pending    # 未完成
list done       # 已完成
```

### 注意

排序直接改了内部 vector。如果想"返回排序后的副本而不改原顺序"，可以拷一份再排。

## 核心重点
- copy_if + lambda 做筛选
- sort + lambda 做排序
- 命令分发越来越多，注意代码组织
- 按日期字符串排序要保证格式统一（YYYY-MM-DD）

## 练习/任务
1. 实现 filterByPriority / filterByDone
2. 实现 sortByPriority / sortByDue
3. 在 main 里加 list prio / list pending 命令
4. （选做）让 list 默认按截止日期排序

## 今日刷题

- **LeetCode 56. 合并区间**。
- **LeetCode 57. 插入区间**。
- **LeetCode 252. 会议室**：按开始时间排序。

## 打卡清单
- [ ] 已实现按优先级筛选
- [ ] 已实现按完成状态筛选
- [ ] 已实现排序
- [ ] 已扩展 list 命令
