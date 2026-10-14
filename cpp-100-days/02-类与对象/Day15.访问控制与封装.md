# Day 15 · 访问控制与封装

## 今日目标
- 掌握 public / private / protected 三种访问权限
- 理解封装的意义：隐藏内部实现，暴露最小接口
- 学会用 setter / getter 控制读写

## 学习内容

### 三种访问修饰符

```cpp
class Account {
private:                         // 私有：只有类内部能访问
    double balance_;
public:                          // 公开：谁都能访问
    Account(double b) : balance_(b) {}

    void deposit(double x) {
        if (x > 0) balance_ += x;
    }
    double balance() const { return balance_; }
};
```

| 修饰符 | 谁能访问 |
|---|---|
| `public` | 类内外都能访问 |
| `private` | 只有类内部的成员函数能访问 |
| `protected` | 类内部 + 派生类（Day26 讲） |

### 为什么要 private

如果余额是 public，外面随便写 `acc.balance_ = -1000;` 就乱套了。把数据藏到 private，只暴露受控的函数：

```cpp
Account acc(100.0);
acc.deposit(50.0);          // 合法
// acc.balance_ = -999;     // 错：private 不可访问
std::cout << acc.balance(); // 只读
```

这就是**封装**：把"怎么做"藏起来，只暴露"做什么"。

### 成员命名约定

工程上常用后缀 `_` 表示私有成员：

- `balance_`、`name_`、`count_`；
- 这样一眼能看出这是成员变量，不是局部变量。

### setter 与 getter

```cpp
class Student {
private:
    std::string name_;
    int age_;
public:
    const std::string& name() const { return name_; }
    void setName(const std::string& n) { name_ = n; }

    int age() const { return age_; }
    void setAge(int a) {
        if (a >= 0 && a < 150) age_ = a;   // 顺手做参数校验
    }
};
```

getter 一般返回 `const T&` 避免拷贝，并加 `const` 表示不改对象。

### 类内成员函数自动 inline

类里直接写出来的成员函数，编译器会当作 inline 候选，不用手动写 `inline`。

## 核心重点
- public 对外公开，private 仅类内可访问
- 封装 = 数据私有 + 暴露受控接口
- 私有成员习惯加 `_` 后缀
- getter 用 `const T&` 返回并加 const 修饰
- 类内定义的成员函数默认可被内联

## 练习/任务
1. 把 Day14 的 Circle 改成 private 半径，提供 setter/getter，setter 拒绝负数
2. 设计一个 `BankAccount` 类：私有余额，提供 deposit、withdraw（余额不足要提示）、balance
3. 写一个 `Rectangle` 类：私有宽高，提供 area()、perimeter()
4. （选做）尝试在 main 里访问 private 成员，观察编译错误

## 打卡清单
- [ ] 已分清 public/private/protected
- [ ] 已理解封装的意义
- [ ] 已会写带校验的 setter
- [ ] 已养成成员变量加 _ 后缀的习惯
