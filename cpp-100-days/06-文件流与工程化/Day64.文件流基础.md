# Day 64 · 文件流基础

## 今日目标
- 学会用 `std::ofstream` 写文件
- 学会用 `std::ifstream` 读文件
- 理解文件打开模式

## 学习内容

### 头文件

```cpp
#include <fstream>
```

三个类：

- `std::ofstream`：写文件；
- `std::ifstream`：读文件；
- `std::fstream`：读写。

### 写文件

```cpp
#include <fstream>
int main() {
    std::ofstream out("hello.txt");
    out << "你好，文件\n";
    out << 42 << '\n';
    out.close();
}
```

ofstream 离开作用域会自动 close，不用手动调。

### 读文件

```cpp
#include <fstream>
#include <iostream>
#include <string>

int main() {
    std::ifstream in("hello.txt");
    if (!in) {
        std::cerr << "打开失败\n";
        return 1;
    }
    std::string line;
    while (std::getline(in, line)) {
        std::cout << line << '\n';
    }
}
```

### 打开模式

| 模式 | 含义 |
|---|---|
| `ios::out` | 写（默认 ofstream） |
| `ios::in` | 读（默认 ifstream） |
| `ios::app` | 追加 |
| `ios::trunc` | 覆盖（默认） |
| `ios::binary` | 二进制 |

```cpp
std::ofstream out("log.txt", std::ios::app);   // 追加
```

### 判断打开成功

```cpp
std::ifstream in("a.txt");
if (!in) { /* 文件不存在 */ }
```

### 错误处理

- 文件不存在：`in` 处于失败状态；
- 读不到内容：`while (in >> x)` 或 `while (getline(...))` 自动结束。

## 核心重点
- ofstream 写，ifstream 读
- `<<` 写文件，`>>` / `getline` 读
- 离开作用域自动 close
- ios::app 追加，ios::binary 二进制
- 打开失败要检查

## 练习/任务
1. 写程序把 1~100 写入 numbers.txt
2. 读 numbers.txt，求和输出
3. 写一个日志程序，每次运行追加一行时间戳
4. （选做）读一个不存在的文件，观察错误处理

## 今日刷题

- **LeetCode 209. 长度最小的子数组**：滑动窗口。
- **LeetCode 3. 无重复字符的最长子串**：滑动窗口。
- **LeetCode 76. 最小覆盖子串**：滑动窗口 Hard。

## 打卡清单
- [ ] 已会写文本文件
- [ ] 已会读文本文件
- [ ] 已会用 ios::app 追加
- [ ] 已完成至少一道滑动窗口题
