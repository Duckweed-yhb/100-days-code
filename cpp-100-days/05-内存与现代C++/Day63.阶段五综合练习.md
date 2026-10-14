# Day 63 · 阶段五综合练习

## 今日目标
- 综合运用智能指针、移动语义、现代 C++ 特性
- 写一个带资源管理的小类
- 复盘本阶段

## 学习内容

### 本阶段知识地图

| 主题 | 关键 |
|---|---|
| 深浅拷贝 | 三/五法则、=default/=delete |
| unique_ptr | 独占所有权 |
| shared_ptr/weak_ptr | 共享、循环引用 |
| 移动语义 | T&&、std::move、移动构造 |
| auto/decltype | 类型推导 |
| range-for/emplace | 现代写法 |
| lambda | 捕获、std::function |
| 异常 | try/throw/catch、std::exception |
| RAII | 资源绑对象生命周期 |

### 综合题目：用智能指针管理 Shape 体系

把 Day37 的 Shape 项目升级一下：

```cpp
#include <memory>
#include <vector>
#include <iostream>

class Shape {
public:
    virtual double area() const = 0;
    virtual ~Shape() = default;
};

class Circle : public Shape {
    double r_;
public:
    Circle(double r) : r_(r) {}
    double area() const override { return 3.14159 * r_ * r_; }
};

int main() {
    std::vector<std::unique_ptr<Shape>> shapes;
    shapes.push_back(std::make_unique<Circle>(1.0));
    shapes.push_back(std::make_unique<Circle>(2.0));

    double total = 0;
    for (const auto& s : shapes) total += s->area();
    std::cout << "总面积：" << total << '\n';
}
```

观察：不用写 delete，vector 销毁时自动释放所有 Shape。

### 复盘建议

- 把"该用哪个智能指针"默写一遍；
- 把五法则写一遍；
- 检查以前写的代码，能不能用 unique_ptr/auto/lambda 简化。

## 核心重点
- 现代 C++ = RAII + 智能指针 + 自动类型推导
- 默认用 unique_ptr，需要共享才用 shared_ptr
- 移动语义让大对象传递几乎免费
- 异常 + RAII 让资源不泄漏
- 下一阶段进入文件流与工程化

## 练习/任务
1. 把 Day37 的 Shape 项目改成用 unique_ptr
2. 写一个类管理动态数组，用 unique_ptr<int[]> 代替裸 new[]
3. 用 auto + lambda 重写一段你以前写的代码
4. （选做）给 Shape 体系加一个异常：面积为负时抛 std::invalid_argument

## 今日刷题

- **LeetCode 79. 单词搜索**：DFS 回溯。
- **LeetCode 51. N 皇后**：经典回溯。
- **LeetCode 200. 岛屿数量**：DFS/BFS 染色。

## 打卡清单
- [ ] 已用 unique_ptr 重写 Shape
- [ ] 已会用现代 C++ 简化旧代码
- [ ] 已完成至少一道回溯题
- [ ] 已复盘本阶段所有知识点
