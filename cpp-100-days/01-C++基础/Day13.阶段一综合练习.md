# Day 13 · 阶段一综合练习

## 今日目标
- 把前 12 天学的基础语法串起来
- 独立完成一个带输入输出、函数、数组、指针的小程序
- 复盘本阶段易错点，为进入面向对象做准备

## 学习内容

### 本阶段知识地图

| 主题 | 关键概念 |
|---|---|
| 环境与编译 | g++、`-std=c++17`、编译型语言 |
| 输入输出 | `std::cin` / `std::cout` / `endl` 与 `\n` |
| 命名空间 | `std::`、`using`、自定义 namespace |
| 类型与常量 | int/double/char/bool、const、列表初始化 |
| 运算符 | 算术、逻辑、位运算、static_cast |
| 流程控制 | if/switch/for/while/do-while、break/continue |
| 函数 | 定义声明、值传递、指针传递、重载、默认参数、inline |
| 引用 | `T&` 别名、`const T&` 传大对象 |
| 数组与字符串 | 数组退化、C 风格串、std::string 初体验 |
| 动态内存 | new/delete、new[]/delete[]、栈与堆 |

### 复习要点：容易踩的坑

1. **`=` 与 `==`**：条件判断里写 `if (x = 5)` 是赋值，不是比较。
2. **整数除法**：`5 / 2 == 2`，要小数写 `5.0 / 2`。
3. **数组越界**：C++ 不帮你检查，越界是未定义行为。
4. **switch 漏 break**：case 会穿透。
5. **返回局部变量地址/引用**：函数一返回局部变量就销毁。
6. **new 与 delete 不配对**：泄漏或崩溃。

### 综合题目：成绩管理小程序

需求：

- 输入 n 个学生的姓名（用 `std::string`）和成绩；
- 计算平均分；
- 输出高于平均分的学生姓名；
- 找最高分和最低分。

参考骨架（自己补全）：

```cpp
#include <iostream>
#include <string>
#include <vector>   // 今天先粗看，后面 STL 阶段会详讲

int main() {
    int n;
    std::cout << "学生人数：";
    std::cin >> n;

    std::vector<std::string> names(n);
    std::vector<double> scores(n);

    for (int i = 0; i < n; ++i) {
        std::cout << "姓名 成绩：";
        std::cin >> names[i] >> scores[i];
    }

    double sum = 0;
    for (double s : scores) sum += s;
    double avg = sum / n;

    std::cout << "平均分：" << avg << '\n';
    std::cout << "高于平均分：\n";
    for (int i = 0; i < n; ++i)
        if (scores[i] > avg) std::cout << names[i] << '\n';
    return 0;
}
```

如果不想用 vector，可以用 `new[]` 自己申请数组。

### 复盘建议

- 把前 12 天的练习代码翻一遍，删掉多余注释、统一命名风格；
- 把"打卡清单"里没打勾的项补完；
- 准备好进入下一阶段：从"面向过程"走向"面向对象"。

## 核心重点
- C++ 基础语法是后面一切的地基，不熟就别往下冲
- 函数 + 数组 + 引用 + 字符串能解决大量实际小问题
- 内存配对（new/delete）、const、引用是现代 C++ 的好习惯
- 下一阶段开始写 class，要把"数据 + 操作数据的函数"捆在一起

## 练习/任务
1. 完成上面的成绩管理小程序，能跑通输入输出
2. 给成绩管理加功能：输入一个姓名，查询该生成绩（找不到要提示）
3. 写一个"猜数字"游戏：程序随机生成 1~100 的数，用户猜，提示大了/小了，直到猜中
4. （选做）用函数把猜数字拆成 `secretNumber()`、`readGuess()`、`judge()` 三个函数

## 打卡清单
- [ ] 已完成成绩管理小程序并跑通
- [ ] 已实现姓名查询功能
- [ ] 已完成猜数字游戏
- [ ] 已把前 12 天代码复盘一遍
