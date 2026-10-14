# Day 66 · 二进制文件与随机读写

## 今日目标
- 区分文本文件与二进制文件
- 学会用 `ios::binary` 读写二进制
- 了解 seekg/seekpt 随机访问

## 学习内容

### 文本 vs 二进制

- 文本文件：人类可读，数字 123 存的是三个字符 '1' '2' '3'；
- 二进制文件：直接存内存里的字节，数字 123 存成 4 字节 int。

二进制更小、更快，但不能用文本编辑器直接看。

### 写二进制

```cpp
#include <fstream>

int main() {
    int data[5] = {1, 2, 3, 4, 5};
    std::ofstream out("data.bin", std::ios::binary);
    out.write(reinterpret_cast<const char*>(data), sizeof(data));
}
```

- `write(指针, 字节数)`；
- 指针要转成 `const char*`；
- 字节数用 sizeof。

### 读二进制

```cpp
int data[5];
std::ifstream in("data.bin", std::ios::binary);
in.read(reinterpret_cast<char*>(data), sizeof(data));
```

### 写结构体

```cpp
struct Point { int x; int y; };
Point p{3, 4};
out.write(reinterpret_cast<const char*>(&p), sizeof(p));
```

注意：如果 struct 里有 string、vector 等指针成员，直接写二进制只会存指针，读回来就是野指针。二进制序列化只用于"纯 POD"数据。

### 随机读写

- `seekg(n)`：读指针移到第 n 字节（get）；
- `seekp(n)`：写指针移到第 n 字节（put）；
- `tellg()`：当前读位置。

```cpp
in.seekg(0, std::ios::end);     // 移到末尾
auto size = in.tellg();          // 文件大小
in.seekg(0);                     // 回到开头
```

### 实际场景

- 图像文件、音频、压缩包都是二进制；
- 数据库存储；
- 大文件随机访问。

## 核心重点
- ios::binary 打开二进制文件
- write/read 配 reinterpret_cast<const char*>
- 二进制只适合纯 POD 数据
- seekg/tellg 随机访问
- 文本文件用 >>/<<，二进制用 read/write

## 练习/任务
1. 写 100 个 int 到二进制文件，再读回来验证
2. 写一个 Point 数组到二进制文件
3. 用 seekg 读取文件大小
4. （选做）写一个简单的"记录式"文件：每条记录固定长度，按序号随机读取

## 今日刷题

- **LeetCode 148. 排序链表**：归并排序。
- **LeetCode 147. 对链表进行插入排序**：插入排序。
- **LeetCode 143. 重排链表**：找中点 + 反转 + 合并。

## 打卡清单
- [ ] 已会用 ios::binary
- [ ] 已会用 read/write
- [ ] 已会用 seekg/tellg
- [ ] 已完成至少一道链表题
