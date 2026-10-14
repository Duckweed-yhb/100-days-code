# Day 61 · 异常处理

## 今日目标
- 学会用 try / throw / catch
- 了解标准异常类层次
- 知道什么时候用异常

## 学习内容

### 为什么需要异常

函数出错时，return 错误码容易被忽略。异常是"出错就抛出，谁合适谁捕获"的机制：

```cpp
double divide(double a, double b) {
    if (b == 0.0) throw std::runtime_error("除数为 0");
    return a / b;
}

int main() {
    try {
        std::cout << divide(1, 0);
    } catch (const std::exception& e) {
        std::cout << "出错：" << e.what() << '\n';
    }
}
```

### try / catch 语法

- `try { ... }` 包裹可能出错的代码；
- `throw 异常对象;` 抛出；
- `catch (类型 名) { ... }` 捕获。

### 标准异常类

```
std::exception
  ├── std::runtime_error
  ├── std::logic_error
  ├── std::bad_alloc
  └── std::bad_cast
```

常用：

- `std::runtime_error("...")`：运行时错误；
- `std::logic_error("...")`：逻辑错误；
- `std::bad_alloc`：new 失败。

### 捕获所有异常

```cpp
catch (...) {
    std::cout << "未知异常\n";
}
```

但一般不推荐，最好知道自己在抓什么。

### 栈展开

throw 之后，程序从当前函数开始一层层退出，局部对象自动析构，直到找到匹配的 catch。这就是 RAII 与异常安全配合的基础。

### 什么时候用异常

- 构造函数无法初始化对象时；
- 库函数失败且返回值不可忽略；
- 错误处理与正常逻辑距离很远。

不是所有错误都要用异常——简单错误用返回值/错误码更清楚。

## 核心重点
- throw 抛异常，try/catch 捕获
- 标准异常都继承 std::exception，用 e.what() 看信息
- 栈展开自动析构局部对象
- 构造函数失败用异常
- 不要用异常替代简单错误检查

## 练习/任务
1. 写函数除零抛异常，main 捕获并打印
2. 写函数 vector 下标越界时抛 std::out_of_range
3. 写一个可能抛多种异常的函数，分别捕获
4. （选做）观察异常抛出时局部对象是否自动析构

## 今日刷题

- **LeetCode 20. 有效的括号**：栈。
- **LeetCode 22. 括号生成**：DFS 回溯。
- **LeetCode 39. 组合总和**：回溯。

## 打卡清单
- [ ] 已会写 try/throw/catch
- [ ] 已会用 std::runtime_error
- [ ] 已理解栈展开
- [ ] 已完成至少一道回溯题
