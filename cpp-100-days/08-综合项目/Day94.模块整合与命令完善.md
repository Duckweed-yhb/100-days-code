# Day 94 · 模块整合与命令完善

## 今日目标
- 把所有命令串起来
- 处理错误输入
- 让程序有完整的用户体验

## 学习内容

### 命令清单

| 命令 | 作用 |
|---|---|
| add | 添加任务 |
| list | 列出全部 |
| list prio | 按优先级排序 |
| list due | 按截止日期排序 |
| list pending | 未完成 |
| list done | 已完成 |
| done <id> | 标记完成 |
| del <id> | 删除 |
| quit | 退出 |

### 输入错误处理

```cpp
if (cmd == "done") {
    int id;
    if (!(std::cin >> id)) {
        std::cout << "用法：done <id>\n";
        std::cin.clear();
        std::cin.ignore(10000, '\n');
        continue;
    }
    if (!store.markDone(id)) std::cout << "没找到 id=" << id << '\n';
}
```

### 帮助命令

```cpp
if (cmd == "help") {
    std::cout << "add / list / done <id> / del <id> / quit\n";
}
```

### 空列表提示

```cpp
const auto& all = store.all();
if (all.empty()) {
    std::cout << "（暂无任务）\n";
    continue;
}
```

### 现在应该跑通的完整流程

```
> add
标题 优先级 截止日期：写报告 1 2026-10-01
已添加，id=1
> list
1 [ ] 写报告 P1 due:2026-10-01
> done 1
> list done
1 [x] 写报告 P1 due:2026-10-01
> quit
```

## 核心重点
- 命令行程序要对错误输入友好
- 每个命令都要有用法提示
- 空状态要给提示
- 整合后整体跑一遍

## 练习/任务
1. 补全所有命令
2. 加 help 命令
3. 处理非法输入（非数字 id、缺参数）
4. （选做）加统计命令：总数、已完成、未完成

## 今日刷题

- **LeetCode 56. 合并区间**。
- **LeetCode 57. 插入区间**。
- **LeetCode 252. 会议室**。

## 打卡清单
- [ ] 所有命令跑通
- [ ] 有 help
- [ ] 错误输入不崩溃
- [ ] 空列表有提示
