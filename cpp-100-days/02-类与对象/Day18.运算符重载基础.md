# Day 18 · 运算符重载基础

## 今日目标
- 理解运算符重载的作用与限制
- 学会把常用运算符写成类的成员函数或全局函数
- 为自定义类型加上直观的语法

## 学习内容

### 为什么要重载运算符

让自定义类型也能用 `+`、`==`、`<<` 这种直观写法，而不是写 `a.add(b)`。

```cpp
struct Vec {
    double x, y;
};

Vec a{1, 2}, b{3, 4};
Vec c = a + b;    // 如果重载了 operator+，就能这么写
```

### 作为成员函数重载

```cpp
class Complex {
    double re_, im_;
public:
    Complex(double r, double i) : re_(r), im_(i) {}

    Complex operator+(const Complex& other) const {
        return Complex(re_ + other.re_, im_ + other.im_);
    }
};

int main() {
    Complex a(1, 2), b(3, 4);
    Complex c = a + b;     // 等价于 a.operator+(b)
}
```

规则：

- 函数名是 `operator+`、`operator-`、`operator==` 等；
- 成员函数重载时，左操作数就是对象本身；
- 不修改对象就加 `const`。

### 作为全局函数重载

有些运算符左操作数不是自定义类型（比如 `std::cout << v`），就得写成全局函数：

```cpp
#include <iostream>

class Complex {
    double re_, im_;
public:
    Complex(double r, double i) : re_(r), im_(i) {}
    double re() const { return re_; }
    double im() const { return im_; }
};

std::ostream& operator<<(std::ostream& os, const Complex& c) {
    return os << c.re() << (c.im() >= 0 ? "+" : "") << c.im() << "i";
}

int main() {
    Complex a(1, 2);
    std::cout << a << '\n';     // 输出 1+2i
}
```

### 常见可重载运算符

| 运算符 | 用途 |
|---|---|
| `+ - * /` | 算术 |
| `== != < >` | 比较 |
| `<< >>` | 流输入输出 |
| `[]` | 下标（Day19） |
| `=` | 赋值（Day19） |

### 不能重载的运算符

`. ` `.*` `::` `?:` `sizeof` `typeid` 等少数几个不能重载，也不要发明新运算符符号。

### 经验法则

- 重载后语义要和内置运算符一致：`+` 就该做加法，别搞成字符串拼接那种奇怪操作；
- 能成员函数就别全局函数；需要隐式转换左操作数时才用全局。

## 核心重点
- 运算符重载让自定义类型能用 `+ == <<` 等直观写法
- 成员函数形式：`T operator+(const T&) const;`
- 流运算符 `<< >>` 必须全局函数，因为左操作数是 ostream
- 不改变运算符原本语义
- 不能重载 `. :: ?: sizeof` 等

## 练习/任务
1. 给 Complex 类重载 `+`、`-`、`==`
2. 给 Complex 重载 `<<`，让它能直接 cout
3. 写一个 `Point` 类，重载 `+`（坐标相加）和 `-`（坐标相减）
4. （选做）给 Point 重载 `==`，判断两点是否相同

## 打卡清单
- [ ] 已会用成员函数重载 operator+
- [ ] 已会用全局函数重载 operator<<
- [ ] 已理解什么时候必须用全局函数
- [ ] 已知道哪些运算符不能重载
