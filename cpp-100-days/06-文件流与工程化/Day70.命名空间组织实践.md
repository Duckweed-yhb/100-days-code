# Day 70 · 命名空间组织实践

## 今日目标
- 用命名空间组织大型项目
- 理解 using 声明的合理使用
- 避免全局命名空间污染

## 学习内容

### 项目命名空间结构

按"组织/模块"分层：

```cpp
// myproj/math/algorithms.h
namespace myproj {
namespace math {
    int add(int a, int b);
}
}
```

使用：

```cpp
myproj::math::add(1, 2);
```

### 嵌套命名空间（C++17 简写）

```cpp
namespace myproj::math {
    int add(int a, int b);
}
```

等价于上面两层嵌套。

### using 的合理位置

- **头文件里不要写 `using namespace std;`**：会污染所有 include 它的文件；
- cpp 文件里可以写，图方便；
- 函数内 using 只在函数作用域有效。

### 命名空间别名

```cpp
namespace mm = myproj::math;
mm::add(1, 2);
```

长名字时很方便。

### 匿名命名空间

.cpp 文件里用 `namespace { ... }` 表示内部链接，相当于 C 的 static：

```cpp
namespace {
    int helper(int x) { return x * 2; }   // 只在本文件可见
}
```

避免和其他文件同名函数冲突。

## 核心重点
- 项目按 `组织::模块` 分层命名空间
- 头文件里别 using namespace std
- C++17 嵌套命名空间简写 `namespace a::b {}`
- 匿名命名空间 = 文件内私有
- 命名空间别名简化长名字

## 练习/任务
1. 把 Day68 的 math_utils 放到 `myproj::math` 命名空间
2. 在 main.cpp 里用 `myproj::math::add` 调用
3. 写一个匿名命名空间里的 helper 函数
4. （选做）在头文件里写 `using namespace std;`，另一个文件 include 它，观察污染

## 今日刷题

- **LeetCode 215. 数组中的第 K 个最大元素**：快速选择或堆。
- **LeetCode 347. 前 K 个高频元素**：堆。
- **LeetCode 4. 寻找两个正序数组的中位数**：Hard 二分。

## 打卡清单
- [ ] 已会分层命名空间
- [ ] 已知道头文件别 using namespace std
- [ ] 已会用匿名命名空间
- [ ] 已完成至少一道中等题
