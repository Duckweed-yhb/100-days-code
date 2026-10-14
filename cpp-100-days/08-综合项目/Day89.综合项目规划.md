# Day 89 · 综合项目规划

## 今日目标
- 选定一个综合项目题目
- 拆解模块、列出功能清单
- 设计类结构

## 学习内容

### 推荐项目：命令行任务管理器

我们选一个能把前面所有知识串起来的项目：**一个命令行任务待办管理器**。

功能：

1. 增：添加任务（标题、截止日期、优先级）；
2. 删：按 id 删除任务；
3. 改：标记任务完成；
4. 查：列出所有、按优先级、按完成状态；
5. 存：启动时从 tasks.csv 加载，退出时保存；
6. 测：关键函数配单元测试。

### 模块拆分

| 模块 | 文件 | 职责 |
|---|---|---|
| Task | task.h/.cpp | 任务数据结构、to_string |
| TaskStore | store.h/.cpp | 增删查改、内存存储 |
| Persistence | persistence.h/.cpp | CSV 读写 |
| Command UI | main.cpp | 命令行交互 |
| Tests | test.cpp | assert 测试 |

### 类设计

```cpp
// Task
struct Task {
    int id;
    std::string title;
    int priority;       // 1~5
    bool done;
    std::string due;    // 2026-10-01
};

// TaskStore
class TaskStore {
    std::vector<Task> tasks_;
    int nextId_ = 1;
public:
    int add(const std::string& title, int priority, const std::string& due);
    bool remove(int id);
    bool markDone(int id);
    std::vector<Task> list() const;
    // ...
};
```

### 用前面学过的东西

- class/封装：Task、TaskStore；
- 智能指针：如需要动态多态；
- STL：vector、map；
- 文件流：CSV 持久化；
- 多文件工程 + CMake；
- 单元测试：assert；
- 现代 C++：auto、lambda、range-for。

### 接下来 12 天安排

- Day89~90：规划 + 骨架；
- Day91~93：核心模块；
- Day94：整合；
- Day95：测试；
- Day96：性能观察；
- Day97：重构；
- Day98：文档与打包；
- Day99：演示复盘；
- Day100：百日总结。

## 核心重点
- 项目 = 把学过的东西用起来
- 先拆模块再写代码
- 数据结构先定下来
- 小步提交，每天能跑

## 练习/任务
1. 画出 Task 和 TaskStore 的类图
2. 写 Task.h 头文件
3. 列一个 TODO 清单（按 Day90~100 分配任务）
4. （选做）想好命令行交互：add/list/done/delete/quit

## 今日刷题

- **LeetCode 200. 岛屿数量**。
- **LeetCode 133. 克隆图**。
- **LeetCode 207. 课程表**。

## 打卡清单
- [ ] 已选定项目
- [ ] 已拆模块
- [ ] 已写 Task.h
- [ ] 已列 Day90~100 任务清单
