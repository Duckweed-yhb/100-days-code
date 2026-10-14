# Day 97 · 重构与代码整洁

## 今日目标
- 审视自己的代码
- 拆分过长函数
- 统一命名与风格

## 学习内容

### 重构是什么

在不改变外部行为的前提下，改善内部结构。有测试兜底，重构才敢动。

### 检查清单

- [ ] 函数是不是太长（>50 行就考虑拆）；
- [ ] 命名是否见名知意；
- [ ] 有没有重复代码；
- [ ] 魔数（3、100 这种）有没有名字；
- [ ] const 是否加够；
- [ ] 有没有裸 new/delete。

### 拆分 main

main 里命令分发太长，拆成函数：

```cpp
void handleAdd(TaskStore& store) { /* ... */ }
void handleList(TaskStore& store) { /* ... */ }
void handleDone(TaskStore& store) { /* ... */ }
void handleDelete(TaskStore& store) { /* ... */ }

int main() {
    TaskStore store;
    Persistence::load(store, "tasks.csv");
    std::string cmd;
    while (std::cin >> cmd) {
        if (cmd == "add") handleAdd(store);
        else if (cmd == "list") handleList(store);
        else if (cmd == "done") handleDone(store);
        else if (cmd == "del") handleDelete(store);
        else if (cmd == "quit") break;
        else std::cout << "未知命令，输入 help\n";
    }
    Persistence::save(store, "tasks.csv");
}
```

### 提取魔数

```cpp
constexpr int DEFAULT_PRIORITY = 3;
constexpr const char* DEFAULT_DUE = "2099-12-31";
```

### 重构后跑测试

每次小重构后跑 `test_todo.exe`，确保没改坏。

## 核心重点
- 重构 = 改善结构不改行为
- 测试是重构的安全网
- 函数短、命名好、不重复
- 小步重构，每步都跑测试
- 魔数要命名

## 练习/任务
1. 把 main 的命令处理拆成函数
2. 给魔数起常量名
3. 检查 const 是否加够
4. （选做）把打印任务的逻辑提取成一个 printTask 函数

## 今日刷题

- **LeetCode 56. 合并区间**。
- **LeetCode 57. 插入区间**。
- **LeetCode 252. 会议室**。

## 打卡清单
- [ ] 已拆分 main
- [ ] 已命名魔数
- [ ] 已检查 const
- [ ] 重构后测试全过
