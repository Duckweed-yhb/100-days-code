# Day 06 · 流程控制

## 今日目标
- 熟练使用 if / switch 分支
- 掌握 for / while / do-while 三种循环
- 会用 break / continue 控制流程

## 学习内容

### if 分支

```cpp
int score;
std::cin >> score;

if (score >= 90) {
    std::cout << "优秀\n";
} else if (score >= 60) {
    std::cout << "及格\n";
} else {
    std::cout << "不及格\n";
}
```

要点：

- 条件表达式可以是 `int`（0 为假，非 0 为真），但推荐写 `bool` 表达式；
- 即使只有一行语句，也建议加大括号 `{}`，避免以后加代码时踩坑。

### switch 多分支

```cpp
char grade;
std::cin >> grade;
switch (grade) {
    case 'A': std::cout << "90+\n"; break;
    case 'B': std::cout << "80+\n"; break;
    case 'C': std::cout << "60+\n"; break;
    default:  std::cout << "其他\n"; break;
}
```

注意：每个 `case` 后要写 `break;`，否则会"穿透"继续执行下一个 case。

### while 循环

```cpp
int n = 100;
int steps = 0;
while (n != 1) {
    if (n % 2 == 0) n /= 2;
    else n = 3 * n + 1;
    ++steps;
}
std::cout << "Collatz 步数：" << steps << '\n';
```

### do-while

至少先执行一次再判断条件：

```cpp
char cmd;
do {
    std::cout << "请输入命令 (q 退出)：";
    std::cin >> cmd;
} while (cmd != 'q');
```

### for 循环

```cpp
int sum = 0;
for (int i = 1; i <= 100; ++i) {
    sum += i;
}
// sum == 5050
```

C++11 起还有范围 for（range-based for），后面学到容器会大量用：

```cpp
int arr[] = {1, 2, 3, 4};
for (int x : arr) {
    std::cout << x << ' ';
}
```

### break 与 continue

- `break`：立刻跳出整个循环；
- `continue`：跳过本次循环剩下的语句，直接进入下一次。

```cpp
for (int i = 1; i <= 10; ++i) {
    if (i % 2 == 0) continue;   // 跳过偶数
    if (i > 7) break;           // 超过 7 就停
    std::cout << i << ' ';      // 输出 1 3 5 7
}
```

### 嵌套循环

打印九九乘法表：

```cpp
for (int i = 1; i <= 9; ++i) {
    for (int j = 1; j <= i; ++j) {
        std::cout << j << '*' << i << '=' << i * j << '\t';
    }
    std::cout << '\n';
}
```

## 核心重点
- `if / else if / else` 分支，条件写括号里
- `switch` 每个 case 别忘 `break;`
- `while` 先判断后执行，`do-while` 先执行后判断
- `for` 用于已知次数的循环
- `break` 跳出整层循环，`continue` 跳过本次

## 练习/任务
1. 输入年份，判断是不是闰年（能被 4 整除但不能被 100 整除，或能被 400 整除）
2. 用 for 循环求 1 到 100 所有偶数之和
3. 打印九九乘法表
4. （选做）输入一个正整数，判断它是不是素数

## 打卡清单
- [ ] 已熟练写 if / switch
- [ ] 已会用三种循环
- [ ] 已分清 break 与 continue
- [ ] 已能写嵌套循环打印图形
