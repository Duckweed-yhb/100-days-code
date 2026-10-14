# Day 01 · 认识C++与环境安装

## 今日目标
- 搞清楚 C++ 是什么、和 C 是什么关系
- 在 Windows 本机装好一套 C++ 编译环境
- 跑通第一个 C++ 程序 `Hello, World`，理解编译运行流程

## 学习内容

### C++ 是什么

C++ 由 Bjarne Stroustrup 于 1979 年前后在贝尔实验室设计，最初叫 "C with Classes"（带类的 C），目的是在 C 的高效底层能力之上引入面向对象。它是一门**编译型、多范式**语言：既可以写面向过程，也可以写面向对象、泛型，现代 C++（C++11 起）还支持 lambda、智能指针、移动语义。

今天为什么还要学 C++：

| 理由 | 说明 |
|---|---|
| 性能天花板 | 几乎所有追求极致性能的领域都用它：游戏引擎、高频交易、数据库、编译器 |
| 底层理解 | 直接操作内存、对象模型、编译期计算，真正搞懂程序怎么跑 |
| 工业生态 | Unreal、MySQL、Chromium、TensorFlow 底层都是 C++ |
| 面试硬通货 | 后端/游戏/嵌入式岗位机试、面试大量围绕 C++ |

### C++ 与 C 的关系

C++ **几乎兼容** C：C 的头文件在 C++ 里仍然能用，但 C++ 有自己更安全的写法。本路线假设你已看过 C 语言基础；即使没看过也没关系，遇到 C 的概念我们会顺带讲清楚。

几个第一眼区别：

- C++ 用 `iostream` 做输入输出，而不是 `printf/scanf`；
- C++ 有 `namespace`（命名空间）避免名字冲突；
- C++ 有 `class`、引用 `&`、函数重载等 C 没有的东西。

### 安装开发环境（Windows）

二选一。

**方案 A：MSYS2 + g++ + VS Code（轻量，推荐）**

1. 安装 [MSYS2](https://www.msys2.org/)；
2. 打开 MSYS2 终端，执行 `pacman -S mingw-w64-x86_64-gcc`；
3. 把 `C:\msys64\mingw64\bin` 加入系统 PATH；
4. VS Code 安装扩展：**C/C++**（微软官方）、**Code Runner**。

**方案 B：Visual Studio 2022 Community（重量级 IDE）**

1. 安装时勾选"使用 C++ 的桌面开发"工作负载；
2. 新建"控制台应用"即可直接写 C++。

验证安装（PowerShell）：

```powershell
g++ --version
```

能输出版本号即安装成功。

### 第一个 C++ 程序

新建文件 `hello.cpp`：

```cpp
#include <iostream>            // 引入标准输入输出头文件

int main() {                   // 程序入口，C++ 里 void 可省略
    std::cout << "Hello, World!" << std::endl;   // 输出一行
    return 0;
}
```

编译并运行：

```powershell
g++ hello.cpp -o hello.exe -std=c++17
.\hello.exe
```

屏幕输出 `Hello, World!` 即成功。

### 几个第一眼语法点

- `#include <iostream>` 引入输入输出库；
- `std::cout` 表示"标准输出"，`<<` 是"把右边的内容送进去"；
- `std::endl` 输出换行并刷新缓冲区；
- `std::` 是命名空间前缀，后面 Day03 会专门讲。

## 核心重点
- C++ 是在 C 之上发展出来的多范式编译型语言，强调高性能与底层控制
- Windows 环境二选一：MSYS2/g++ 或 Visual Studio 2022
- 编译命令 `g++ 源文件.cpp -o 可执行名 -std=c++17`
- `#include <iostream>` + `std::cout << ...` 是 C++ 的输出方式
- 程序入口是 `int main()`，`return 0` 表示正常退出

## 练习/任务
1. 安装好编译器，执行 `g++ --version` 确认版本号
2. **手敲**（不要复制粘贴）`hello.cpp` 并编译运行成功
3. 让程序输出两行：第一行 `Hello, C++!`，第二行你自己的名字
4. （选做）把 `std::endl` 换成 `'\n'` 再编译运行，观察结果是否相同

## 打卡清单
- [ ] 已安装 g++ 或 Visual Studio，`g++ --version` 有输出
- [ ] 已编译运行第一个 C++ 程序并看到 `Hello, World!`
- [ ] 已尝试修改输出内容并重新编译运行
- [ ] 已理解 C++ 与 C 的基本关系
