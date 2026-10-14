# Day 41 · string字符串

## 今日目标
- 熟练使用 `std::string`
- 掌握常用操作：拼接、查找、子串、转换
- 了解 string 与 C 风格字符串的互转

## 学习内容

### 构造与初始化

```cpp
#include <string>

std::string s1;                  // 空串
std::string s2 = "hello";
std::string s3("world");
std::string s4(5, 'a');          // "aaaaa"
```

### 常用操作

| 操作 | 说明 |
|---|---|
| `s.size()` / `s.length()` | 长度 |
| `s.empty()` | 是否空 |
| `s + t` | 拼接 |
| `s += t` | 追加 |
| `s == t` / `s < t` | 字典序比较 |
| `s[i]` | 取字符 |
| `s.substr(pos, len)` | 取子串 |
| `s.find(t)` | 查找，找不到返回 `string::npos` |
| `s.replace(pos, len, t)` | 替换 |
| `s.insert(pos, t)` | 插入 |
| `s.erase(pos, len)` | 删除 |

### 示例

```cpp
std::string s = "hello world";
std::cout << s.size();              // 11
std::cout << s.substr(0, 5);       // hello
std::cout << s.find("world");      // 6
if (s.find("xyz") == std::string::npos) {
    std::cout << "没找到\n";
}
s += "!";                            // "hello world!"
```

### 与数值互转

```cpp
std::string a = std::to_string(42);        // int -> string
std::string b = std::to_string(3.14);
int x = std::stoi("123");                  // string -> int
double y = std::stod("3.14");
```

### 与 C 风格字符串互转

```cpp
std::string s = "hello";
const char* c = s.c_str();     // string -> const char*
std::string t(c);              // const char* -> string
```

### 读一行

```cpp
std::string line;
std::getline(std::cin, line);   // 读一整行（含空格）
```

注意：`cin >> s` 遇到空格就停；要读带空格的整行用 `getline`。

## 核心重点
- string 自动管理内存，别用 char*
- size / substr / find / += / replace 最常用
- find 找不到返回 `string::npos`
- to_string / stoi / stod 做数值转换
- getline 读整行

## 练习/任务
1. 读入一个字符串，统计其中元音字母个数
2. 读入一个英文句子，按空格切成单词，存在 vector<string> 里
3. 实现整数转字符串（不用 to_string）
4. （选做）判断一个字符串是不是回文

## 今日刷题

- **LeetCode 3. 无重复字符的最长子串**：滑动窗口，用 set 或数组记录窗口内字符。
- **LeetCode 125. 验证回文串**：双指针跳过非字母数字，统一大小写后比较。
- **LeetCode 5. 最长回文子串**：中心扩展，枚举每个中心向两边扩。

## 打卡清单
- [ ] 已熟练使用 string 常用接口
- [ ] 已会用 find/substr
- [ ] 已会用 to_string/stoi
- [ ] 已完成至少一道字符串刷题
