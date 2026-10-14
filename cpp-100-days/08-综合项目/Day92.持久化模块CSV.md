# Day 92 · 持久化模块CSV

## 今日目标
- 实现从 CSV 加载任务
- 实现保存任务到 CSV
- 启动时自动加载，退出时自动保存

## 学习内容

### CSV 格式

```
id,title,priority,done,due
1,买菜,3,0,2026-10-01
2,写报告,1,1,2026-09-30
```

### persistence.h

```cpp
#include "store.h"

class Persistence {
public:
    static void save(const TaskStore& store, const std::string& path);
    static void load(TaskStore& store, const std::string& path);
};
```

### save 实现

```cpp
#include <fstream>
#include "persistence.h"

void Persistence::save(const TaskStore& store, const std::string& path) {
    std::ofstream out(path);
    out << "id,title,priority,done,due\n";
    for (const auto& t : store.all()) {
        out << t.id << ',' << t.title << ','
            << t.priority << ',' << (t.done ? 1 : 0) << ','
            << t.due << '\n';
    }
}
```

### load 实现

```cpp
void Persistence::load(TaskStore& store, const std::string& path) {
    std::ifstream in(path);
    if (!in) return;
    std::string line;
    std::getline(in, line);   // 跳过表头
    while (std::getline(in, line)) {
        std::istringstream iss(line);
        std::string cell;
        std::vector<std::string> cells;
        while (std::getline(iss, cell, ',')) cells.push_back(cell);
        if (cells.size() < 5) continue;
        Task t;
        t.id = std::stoi(cells[0]);
        t.title = cells[1];
        t.priority = std::stoi(cells[2]);
        t.done = (cells[3] == "1");
        t.due = cells[4];
        store.addLoaded(t);   // 直接加，不分配新 id
    }
}
```

需要在 TaskStore 里加一个 `addLoaded(Task)`，并维护 nextId_。

### main 集成

```cpp
int main() {
    TaskStore store;
    Persistence::load(store, "tasks.csv");
    // ... 命令循环 ...
    Persistence::save(store, "tasks.csv");
}
```

## 核心重点
- CSV 用逗号分隔
- 表头第一行
- load 要处理文件不存在
- save/load 与 Store 解耦

## 练习/任务
1. 完成 save 和 load
2. 在 main 启动时加载，退出时保存
3. 验证：加任务 -> 退出 -> 再启动，任务还在
4. （选做）处理标题里带逗号的情况（用引号包裹）

## 今日刷题

- **LeetCode 200. 岛屿数量**。
- **LeetCode 133. 克隆图**。
- **LeetCode 207. 课程表**。

## 打卡清单
- [ ] 已实现 CSV 保存
- [ ] 已实现 CSV 加载
- [ ] 已验证重启后数据还在
- [ ] 已处理文件不存在
