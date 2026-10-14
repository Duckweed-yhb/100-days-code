# Day 05 · 分支结构if与switch

## 今日目标
- 掌握 `if`、`if-else`、`else if` 多分支写法
- 理解 `switch-case` 的用法与 `break` 的作用
- 能编写根据条件做不同处理的程序

## 学习内容

### if 语句

```c
if (条件) {
    // 条件为真时执行
}
```

### if-else

```c
int score = 75;
if (score >= 60) {
    printf("及格\n");
} else {
    printf("不及格\n");
}
```

### else if 多分支

```c
if (score >= 90) {
    printf("优秀\n");
} else if (score >= 80) {
    printf("良好\n");
} else if (score >= 60) {
    printf("及格\n");
} else {
    printf("不及格\n");
}
```

注意：`else if` 本质是 `else { if (...) }` 的简写，条件从上到下匹配，一旦命中就不再往下判断。

### 嵌套 if

```c
if (age >= 18) {
    if (has_ticket) {
        printf("可以进入\n");
    } else {
        printf("请先买票\n");
    }
}
```

**缩进与大括号风格**：即使只有一条语句，也建议写大括号，避免后续加语句时漏写导致逻辑错误。

### 三目运算符

```c
int max = (a > b) ? a : b;
```

等价于：

```c
int max;
if (a > b) max = a;
else       max = b;
```

三目运算符适合简单二选一，复杂逻辑还是用 if-else 更清晰。

### switch 语句

```c
switch (day) {
    case 1:
        printf("星期一\n");
        break;
    case 2:
        printf("星期二\n");
        break;
    case 6:
    case 7:
        printf("周末\n");
        break;
    default:
        printf("输入无效\n");
        break;
}
```

要点：
- `switch` 括号里只能是整型/字符型表达式，**不能是浮点数或字符串**
- `case` 后必须是常量，不能用变量
- **`break` 不能漏**：漏掉会"贯穿"（fall through），继续执行下一个 case 的代码
- `default` 处理所有未匹配的情况，位置任意，建议放最后
- 多个 case 共用同一段代码时可以故意省略 `break`（如上例 case 6/7）

### if 与 switch 怎么选

- 分支是等值判断且取值离散 → 用 `switch`
- 分支是范围判断（如分数段）→ 用 `else if`

## 核心重点
- `if` 条件为"非零即真"，浮点数与 0 比较要用 `fabs(x) < 1e-9`
- `else if` 从上往下匹配，命中即停
- `switch` 只支持整型/字符等值判断，每个 case 末尾记得 `break`
- 三目运算符适合简单二选一
- 大括号即使一行也建议写上

## 练习/任务
1. 输入一个整数，判断它是奇数还是偶数
2. 输入百分制成绩，输出五级制（优 90+、良 80+、中 70+、及格 60+、不及格）
3. 输入年份和月份，输出该月有多少天（考虑闰年：能被 4 整除但不能被 100 整除，或能被 400 整除）
4. （选做）用 switch 写一个简易计算器：输入两个数和一个运算符 `+ - * /`，输出结果

## 打卡清单
- [ ] 熟练使用 if-else if-else 多分支
- [ ] 理解 switch-case 中 break 的作用
- [ ] 能判断闰年等嵌套条件逻辑
- [ ] 知道 if 与 switch 的适用场景
