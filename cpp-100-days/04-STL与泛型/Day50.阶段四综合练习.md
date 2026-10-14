# Day 50 · 阶段四综合练习

## 今日目标
- 综合使用 STL 容器与算法
- 写一个小型"通讯录"程序
- 复盘本阶段所有容器

## 学习内容

### 本阶段知识地图

| 主题 | 关键 |
|---|---|
| 模板 | 函数模板、类模板、编译期实例化 |
| vector | 动态数组，默认首选 |
| string | 自动内存管理 |
| deque/list | 头尾 O(1) / 链表 |
| stack/queue | LIFO / FIFO |
| priority_queue | 堆，Top K |
| set/map | 有序关联容器 |
| unordered_map | 哈希，O(1) |
| 迭代器 | begin/end、失效 |
| algorithm | sort/find/accumulate/unique |
| lambda | `[=]`/`[&]` |

### 综合项目：通讯录

需求：

- 每条记录：姓名（string）、电话（string）、邮箱（string）；
- 功能：添加、按姓名查找、按姓名排序、打印所有、删除；
- 用 `std::map<string, Record>` 存，自动按姓名排序。

参考骨架：

```cpp
#include <iostream>
#include <string>
#include <map>

struct Record {
    std::string phone;
    std::string email;
};

class ContactBook {
    std::map<std::string, Record> book_;
public:
    void add(const std::string& name, const Record& r) {
        book_[name] = r;
    }
    void printAll() const {
        for (const auto& [name, r] : book_) {
            std::cout << name << " | " << r.phone << " | " << r.email << '\n';
        }
    }
    bool find(const std::string& name, Record& out) const {
        auto it = book_.find(name);
        if (it == book_.end()) return false;
        out = it->second;
        return true;
    }
    void remove(const std::string& name) { book_.erase(name); }
};
```

### 复盘建议

- 把所有容器对比表默写一遍：什么时候用哪个；
- 把 algorithm 常用函数挑出来手写一遍；
- 下一阶段进入内存与现代 C++，会解释为什么需要智能指针。

## 核心重点
- STL 是 C++ 程序员的武器库
- 容器选择：默认 vector，需要键值用 map，需要快速查找用 unordered_map
- algorithm + lambda 能省大量手写循环
- 模板是 STL 的基础

## 练习/任务
1. 完成上面的 ContactBook，实现增删查改
2. 加一个功能：按手机号查找（用遍历或单独建 unordered_map）
3. 读入 10 个联系人，按姓名排序打印
4. （选做）把记录存到 vector 里，自己用 sort + lambda 按姓名排序

## 今日刷题

- **LeetCode 15. 三数之和**：排序 + 双指针，注意去重。
- **LeetCode 49. 字母异位词分组**：把每个字符串排序后当 key，vector 当 value。
- **LeetCode 56. 合并区间**：按起点排序，遍历合并重叠区间。

## 打卡清单
- [ ] 已完成 ContactBook
- [ ] 已能默写容器选择表
- [ ] 已完成至少一道中等刷题
- [ ] 已复盘本阶段所有算法
