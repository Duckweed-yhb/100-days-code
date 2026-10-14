# Day 95 · 单元测试补全

## 今日目标
- 给 TaskStore 关键函数写测试
- 让 test_todo 能跑
- 养成"改完跑测试"的习惯

## 学习内容

### test.cpp 结构

```cpp
#include <cassert>
#include <iostream>
#include "store.h"

static void testAdd() {
    TaskStore s;
    int id1 = s.add("买菜", 3, "2026-10-01");
    int id2 = s.add("写报告", 1, "2026-09-30");
    assert(s.size() == 2);
    assert(id1 == 1);
    assert(id2 == 2);
}

static void testRemove() {
    TaskStore s;
    int id = s.add("买菜", 3, "2026-10-01");
    assert(s.remove(id) == true);
    assert(s.size() == 0);
    assert(s.remove(id) == false);
}

static void testMarkDone() {
    TaskStore s;
    int id = s.add("买菜", 3, "2026-10-01");
    assert(s.markDone(id) == true);
    assert(s.all()[0].done);
}

int main() {
    testAdd();
    testRemove();
    testMarkDone();
    std::cout << "所有测试通过\n";
}
```

### 测试覆盖

至少测：

- add 后 size 正确、id 递增；
- remove 存在/不存在；
- markDone 存在/不存在；
- CSV 保存后再加载，数据一致（round-trip）。

### 跑测试

```powershell
cmake --build .
.\test_todo.exe
```

输出 `所有测试通过` 即可。

### 测试驱动的修改

以后改任何代码，先跑测试。测试挂了立刻知道。

## 核心重点
- 关键函数都要有测试
- assert 简单够用
- round-trip 测试持久化
- 改代码后先跑测试

## 练习/任务
1. 写 testAdd / testRemove / testMarkDone
2. 写 CSV round-trip 测试：存 3 个任务，加载回来对比
3. 故意改坏一个函数，看测试是否报错
4. （选做）加边界测试：删不存在的 id

## 今日刷题

- **LeetCode 56. 合并区间**。
- **LeetCode 57. 插入区间**。
- **LeetCode 252. 会议室**。

## 打卡清单
- [ ] 已写 add/remove/done 测试
- [ ] 已写 CSV round-trip
- [ ] 测试全部通过
- [ ] 已体验"改坏 -> 测试报错"
