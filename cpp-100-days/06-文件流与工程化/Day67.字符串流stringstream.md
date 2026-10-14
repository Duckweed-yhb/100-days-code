# Day 67 · 字符串流stringstream

## 今日目标
- 学会用 `std::ostringstream` / `istringstream`
- 在内存中做格式化与解析
- 替代 sprintf 与手动字符串拼接

## 学习内容

### 是什么

`<sstream>` 提供把 string 当文件流读写的能力：

- `istringstream`：从 string 读；
- `ostringstream`：往 string 写；
- `stringstream`：读写都可以。

### 拼接字符串

```cpp
#include <sstream>

std::ostringstream oss;
oss << "姓名：" << "张三" << '\n';
oss << "年龄：" << 20 << '\n';
std::string result = oss.str();
```

比 `s += "年龄" + to_string(20)` 干净。

### 解析字符串

```cpp
std::string input = "1 3.14 hello";
std::istringstream iss(input);
int a;
double b;
std::string c;
iss >> a >> b >> c;
// a=1, b=3.14, c="hello"
```

自动按空白切分并转换类型。

### 按分隔符切

```cpp
std::string line = "apple,banana,cat";
std::istringstream iss(line);
std::string token;
while (std::getline(iss, token, ',')) {
    std::cout << token << '\n';
}
```

### 数字转字符串

```cpp
std::ostringstream oss;
oss << 42;
std::string s = oss.str();
```

当然也可以直接用 `std::to_string`，但 stringstream 更灵活。

## 核心重点
- ostringstream 在内存中拼接
- istringstream 在内存中解析
- 比 sprintf / 手动拼字符串安全
- getline(iss, token, ',') 按分隔符切
- 文件流与字符串流接口一致

## 练习/任务
1. 用 ostringstream 拼接一个多行日志
2. 用 istringstream 解析一行 "1 3.14 hello"
3. 用 getline + istringstream 切分 CSV
4. （选做）用 stringstream 实现字符串与任意类型互转

## 今日刷题

- **LeetCode 165. 比较版本号**：按点切分，逐段比较。
- **LeetCode 93. 复原 IP 地址**：回溯切分。
- **LeetCode 71. 简化路径**：栈处理路径。

## 打卡清单
- [ ] 已会用 ostringstream 拼接
- [ ] 已会用 istringstream 解析
- [ ] 已会按分隔符切分
- [ ] 已完成至少一道字符串题
