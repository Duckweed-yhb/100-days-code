# Day 48 · STL算法库

## 今日目标
- 学会使用 `<algorithm>` 里的常用算法
- 掌握 sort/find/count/accumulate 等
- 减少自己手写循环

## 学习内容

### 引入 algorithm

```cpp
#include <algorithm>
#include <numeric>   // accumulate 在这里
```

### sort 排序

```cpp
std::vector<int> v{3, 1, 4, 1, 5, 9, 2, 6};
std::sort(v.begin(), v.end());                 // 升序
std::sort(v.begin(), v.end(), std::greater<int>());   // 降序
```

### find / count

```cpp
auto it = std::find(v.begin(), v.end(), 5);
if (it != v.end()) std::cout << "找到了\n";

int c = std::count(v.begin(), v.end(), 1);   // 1 出现几次
```

### 常用算法速查

| 算法 | 作用 |
|---|---|
| `sort(begin, end)` | 排序 |
| `reverse(begin, end)` | 翻转 |
| `find(begin, end, x)` | 查找 |
| `count(begin, end, x)` | 计数 |
| `accumulate(begin, end, init)` | 求和 |
| `max_element(begin, end)` | 最大值迭代器 |
| `min_element(begin, end)` | 最小值迭代器 |
| `fill(begin, end, x)` | 填充 |
| `copy(src.begin, src.end, dst.begin)` | 拷贝 |
| `unique(begin, end)` | 去重（需先 sort） |
| `lower_bound(begin, end, x)` | 二分查找 |

### accumulate 求和

```cpp
#include <numeric>
int sum = std::accumulate(v.begin(), v.end(), 0);
double avg = (double)sum / v.size();
```

### max_element

```cpp
auto it = std::max_element(v.begin(), v.end());
if (it != v.end()) std::cout << *it;
```

### unique 去重

`unique` 不会真正删除元素，只是把重复的移到后面，返回新尾迭代器：

```cpp
std::sort(v.begin(), v.end());
v.erase(std::unique(v.begin(), v.end()), v.end());
```

### 自定义比较

```cpp
std::sort(v.begin(), v.end(), [](int a, int b) {
    return a > b;     // 降序
});
```

lambda 下一天专门讲。

## 核心重点
- sort / find / count / accumulate 最常用
- max_element 返回迭代器，要解引用
- unique 要先 sort，再 erase
- 算法作用于迭代器区间，与具体容器解耦
- 能用算法就别手写循环

## 练习/任务
1. 用 sort 对 vector 升序、降序各排一次
2. 用 accumulate 求和、求平均
3. 读入 n 个数，去重后输出（sort + unique + erase）
4. （选做）用 max_element 找最大值及其下标

## 今日刷题

- **LeetCode 88. 合并两个有序数组**：从后往前双指针。
- **LeetCode 75. 颜色分类**：荷兰国旗问题，三指针一趟扫描。
- **LeetCode 169. 多数元素**：排序后取中间，或 Boyer-Moore 投票。

## 打卡清单
- [ ] 已会用 sort/find/count
- [ ] 已会用 accumulate
- [ ] 已会用 unique 去重
- [ ] 已完成至少一道排序刷题
