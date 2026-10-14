# Day 91 · 核心模块Task与Store

## 今日目标
- 实现 Task 的基本方法
- 实现 TaskStore 的增删查改
- 跑通命令行 add/list

## 学习内容

### task.h 补全

```cpp
struct Task {
    int id = 0;
    std::string title;
    int priority = 3;
    bool done = false;
    std::string due;

    std::string toString() const;
};
```

### store.h

```cpp
#include <vector>
#include "task.h"

class TaskStore {
    std::vector<Task> tasks_;
    int nextId_ = 1;
public:
    int add(const std::string& title, int priority, const std::string& due);
    bool remove(int id);
    bool markDone(int id);
    const std::vector<Task>& all() const { return tasks_; }
    size_t size() const { return tasks_.size(); }
};
```

### store.cpp 关键实现

```cpp
int TaskStore::add(const std::string& title, int priority, const std::string& due) {
    Task t;
    t.id = nextId_++;
    t.title = title;
    t.priority = priority;
    t.due = due;
    tasks_.push_back(std::move(t));
    return t.id;
}

bool TaskStore::remove(int id) {
    for (auto it = tasks_.begin(); it != tasks_.end(); ++it) {
        if (it->id == id) {
            tasks_.erase(it);
            return true;
        }
    }
    return false;
}
```

### main.cpp 命令循环

```cpp
#include <iostream>
#include "store.h"

int main() {
    TaskStore store;
    std::string cmd;
    while (true) {
        std::cout << "> ";
        std::cin >> cmd;
        if (cmd == "quit") break;
        if (cmd == "add") {
            std::string title, due;
            int pri;
            std::cout << "标题 优先级 截止日期：";
            std::cin >> title >> pri >> due;
            int id = store.add(title, pri, due);
            std::cout << "已添加，id=" << id << '\n';
        } else if (cmd == "list") {
            for (const auto& t : store.all()) {
                std::cout << t.id << " [" << (t.done ? "x" : " ") << "] "
                          << t.title << " P" << t.priority
                          << " due:" << t.due << '\n';
            }
        }
    }
}
```

## 核心重点
- 数据结构先定好
- Store 封装 vector，对外暴露最小接口
- 命令行循环：读命令 -> 分发
- 每天跑通一个命令再加下一个

## 练习/任务
1. 完成 Task::toString
2. 完成 TaskStore::add / remove / markDone
3. 实现 add/list 命令
4. （选做）加 delete 和 done 命令

## 今日刷题

- **LeetCode 200. 岛屿数量**。
- **LeetCode 133. 克隆图**。
- **LeetCode 207. 课程表**。

## 打卡清单
- [ ] 已实现 TaskStore
- [ ] 已能 add 任务
- [ ] 已能 list 任务
- [ ] 已能删除/标记完成
