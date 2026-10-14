# Day 90 · 命令行计算器

## 今日目标
- 实现一个支持加减乘除的命令行计算器
- 练习字符串解析
- 巩固多文件编译

## 学习内容

### 需求

用户输入 `3 + 5`，输出 8。支持 `+ - * /`。

### 核心代码

```c
#include <stdio.h>

int main(void) {
    double a, b;
    char op;
    printf("输入表达式（如 3 + 5）: ");
    if (scanf("%lf %c %lf", &a, &op, &b) != 3) {
        printf("格式错误\n");
        return 1;
    }

    double result;
    switch (op) {
        case '+': result = a + b; break;
        case '-': result = a - b; break;
        case '*': result = a * b; break;
        case '/':
            if (b == 0) { printf("除零\n"); return 1; }
            result = a / b;
            break;
        default:
            printf("未知运算符\n");
            return 1;
    }
    printf("= %.2f\n", result);
    return 0;
}
```

### 扩展：支持连续计算

```c
while (1) {
    printf("> ");
    double a, b; char op;
    if (scanf("%lf %c %lf", &a, &op, &b) != 3) break;
    // ... 计算打印
}
```

### 扩展：表达式求值（栈）

支持 `3 + 4 * 2` 这种带优先级的表达式，需要两个栈（操作数栈、运算符栈）。这是编译原理入门，今天先做简单版。

## 核心重点
- scanf 解析表达式
- switch 分运算符
- 检查除零
- 循环交互
- 错误处理返回值

## 练习/任务
1. 实现基础计算器
2. 支持连续输入直到输入 q
3. 加错误处理（除零、非法输入）
4. （选做）支持括号表达式

## 今日刷题
- 今天项目为主，不安排算法题。

## 打卡清单
- [ ] 实现基础计算器
- [ ] 会解析输入
- [ ] 处理错误情况
- [ ] 巩固多文件编译
