# Day 65 · 文本文件读写实战

## 今日目标
- 读写 CSV / 简单配置文件
- 理解格式化输入输出
- 写一个能持久化的小程序

## 学习内容

### 读 CSV

假设有 `scores.csv`：

```
张三,90
李四,85
王五,95
```

```cpp
#include <fstream>
#include <sstream>
#include <string>
#include <vector>
#include <iostream>

struct Record { std::string name; double score; };

int main() {
    std::ifstream in("scores.csv");
    std::vector<Record> records;
    std::string line;
    while (std::getline(in, line)) {
        std::istringstream iss(line);
        std::string name;
        double score;
        std::getline(iss, name, ',');
        iss >> score;
        records.push_back({name, score});
    }
    for (const auto& r : records)
        std::cout << r.name << ": " << r.score << '\n';
}
```

### 写 CSV

```cpp
std::ofstream out("out.csv");
out << "name,score\n";
out << "张三,90\n";
out << "李四,85\n";
```

### 格式化输出

```cpp
#include <iomanip>
out << std::fixed << std::setprecision(2) << 3.14159;   // 3.14
out << std::setw(10) << "hello";                          // 宽度 10
```

### 配置文件

简单 key=value 配置：

```
server=127.0.0.1
port=8080
```

```cpp
std::ifstream in("config.ini");
std::string line;
while (std::getline(in, line)) {
    auto pos = line.find('=');
    if (pos == std::string::npos) continue;
    auto key = line.substr(0, pos);
    auto val = line.substr(pos + 1);
    // ...
}
```

### 常见坑

- 中文编码：Windows 默认 GBK，跨平台注意 UTF-8；
- 行尾换行：Windows `\r\n`，Linux `\n`；
- 文件路径：Windows 里写字符串要 `"C:\\dir\\a.txt"`。

## 核心重点
- 按行读 + istringstream 解析 CSV
- getline(iss, field, ',') 按分隔符切
- iomanip 控制格式
- 简单配置文件自己解析即可
- Windows 路径用双反斜杠

## 练习/任务
1. 写程序把 5 个学生成绩写入 CSV
2. 读 CSV 并计算平均分
3. 写一个 key=value 配置文件读取器
4. （选做）用 iomanip 让输出对齐

## 今日刷题

- **LeetCode 560. 和为 K 的子数组**：前缀和 + 哈希。
- **LeetCode 523. 连续数组**：前缀和 + 哈希。
- **LeetCode 238. 除自身以外数组的乘积**：前缀积 + 后缀积。

## 打卡清单
- [ ] 已会读写 CSV
- [ ] 已会用 istringstream 切分
- [ ] 已会用 iomanip 格式化
- [ ] 已完成至少一道前缀和题
