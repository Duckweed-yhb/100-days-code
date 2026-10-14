# Day 74 · 调试技巧

## 今日目标
- 学会用打印调试
- 了解 gdb / VS 调试器基本用法
- 识别常见崩溃错误

## 学习内容

### 打印调试

最朴素也最有效：

```cpp
std::cerr << "进入函数 f, x=" << x << '\n';
```

用 `std::cerr` 而不是 `cout`，因为 cerr 无缓冲，立刻输出。

### 常见崩溃错误

| 错误 | 原因 |
|---|---|
| 段错误 / Access violation | 空指针、越界、delete 后使用 |
| 双重 free | 两个对象析构同一块内存 |
| 未定义行为 | 用未初始化变量、越界 |
| 栈溢出 | 递归太深、局部数组太大 |

### gdb 基本命令

Linux/MSYS2 下：

```powershell
g++ -g app.cpp -o app
gdb app
```

常用：

- `break main`：在 main 打断点；
- `run`：运行；
- `next`：单步跳过；
- `step`：单步进入；
- `print x`：打印变量；
- `continue`：继续。

Windows VS 里直接按 F5 调试，鼠标悬停看变量。

### 编译选项

```powershell
g++ -g -O0 -Wall -Wextra app.cpp -o app
```

- `-g`：生成调试信息；
- `-O0`：不优化，方便调试。

### 断言防呆

```cpp
if (ptr == nullptr) throw std::runtime_error("空指针");
```

提前在错误现场崩溃，比后面莫名其妙崩好查。

## 核心重点
- 打印调试用 cerr
- -g -O0 方便调试
- 段错误多半是指针/越界
- VS 直接 F5，命令行用 gdb
- 提前 assert 比事后崩溃好

## 练习/任务
1. 故意写一个空指针解引用，用调试器定位
2. 在 gdb/VS 里单步走过一个循环
3. 用 print 命令查看变量
4. （选做）制造一个越界错误，用调试器找到出错行

## 今日刷题

- **LeetCode 133. 克隆图**：DFS/BFS + 哈希表。
- **LeetCode 207. 课程表**：拓扑排序 BFS。
- **LeetCode 210. 课程表 II**：拓扑排序输出顺序。

## 打卡清单
- [ ] 已会用 -g 编译调试版
- [ ] 已会在 VS 或 gdb 里打断点
- [ ] 已能定位空指针/越界
- [ ] 已完成至少一道图论题
