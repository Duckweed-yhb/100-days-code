# Day 01 · 认识C语言与环境安装

## 今日目标
- 搞清 C 语言是什么、50 多年了为什么还在用
- 在本机装好一套 C 开发环境（gcc 或 Visual Studio 二选一）
- 跑通第一个 C 程序 "Hello, World"，理解"编译 → 运行"的完整流程

## 学习内容

### C 语言是什么

C 语言由 Dennis Ritchie 于 1972 年前后在贝尔实验室设计，用来重写 UNIX 操作系统。它是一门**编译型、过程式**语言：贴近硬件、运行效率高、能直接操作内存，至今仍是操作系统、嵌入式、数据库、编译器等领域的事实标准语言。

为什么今天还要学 C：

| 理由 | 说明 |
|---|---|
| 底层能力 | 通过指针直接操作内存，真正理解计算机怎么工作 |
| 性能标杆 | 几乎所有语言都要拿性能和 C 比 |
| 算法基础 | 竞赛与面试常以 C 打算法与数据结构基础，考研/保研机试主流语言 |
| 生态地基 | Linux 内核、Windows 内核、Python/Java 虚拟机大量由 C 编写 |

### 安装开发环境（Windows）

二选一即可。

**方案 A：MinGW-w64 + VS Code（轻量，推荐）**

1. 安装 [MSYS2](https://www.msys2.org/)；
2. 打开 MSYS2 终端，执行 `pacman -S mingw-w64-x86_64-gcc`；
3. 把 `C:\msys64\mingw64\bin` 加入系统 PATH；
4. VS Code 安装扩展：**C/C++**（微软官方）、**Code Runner**。

**方案 B：Visual Studio 2022 Community（重量级 IDE）**

1. 安装时勾选"使用 C++ 的桌面开发"工作负载；
2. 新建"控制台应用"，把源文件后缀改为 `.c` 即可按 C 语法编写。

验证安装（PowerShell / CMD）：

```powershell
gcc --version
```

能输出版本号即安装成功。

### 第一个 C 程序

在 VS Code 新建文件 `hello.c`：

```c
#include <stdio.h>               // 引入标准输入输出头文件

int main(void)                   // 主函数：程序入口
{
    printf("Hello, World!\n");   // 打印一行文本，\n 表示换行
    return 0;                    // 返回 0 表示正常结束
}
```

编译并运行：

```powershell
gcc hello.c -o hello.exe
.\hello.exe
```

屏幕输出 `Hello, World!` 即成功。

### 理解"编译 → 运行"

C 是编译型语言，执行分两步：源代码（`.c`）经**编译器**（gcc）翻译成机器码可执行文件（`.exe`），再被**运行**。每次修改代码都要重新编译，这点与 Python、JavaScript 这类解释型/即时编译语言不同，也是 C 性能高的原因之一。

## 核心重点
- C 是编译型、过程式、贴近硬件的系统级语言，程序入口是 `main` 函数
- `#include <stdio.h>` 引入标准输入输出库，`printf` 用于输出文本
- `\n` 是换行符；`return 0;` 表示程序正常结束
- 编译命令 `gcc 源文件.c -o 输出名`，运行生成的可执行文件
- Windows 装环境二选一：MSYS2/gcc 或 Visual Studio，装完用 `gcc --version` 验证

## 练习/任务
1. 安装好编译器，执行 `gcc --version` 确认版本号
2. **手敲**（不要复制粘贴）`hello.c` 并编译运行成功
3. 修改程序输出两行：第一行 `Hello, World!`，第二行你自己的名字
4. （选做）把 `\n` 删掉再编译运行，观察输出变化，想想为什么

## 打卡清单
- [ ] 已安装 gcc 或 Visual Studio，`gcc --version` 有输出
- [ ] 已编译运行第一个 C 程序并看到 `Hello, World!`
- [ ] 已尝试修改输出内容并重新编译运行
- [ ] 已理解编译型语言"编译 → 运行"的基本流程
