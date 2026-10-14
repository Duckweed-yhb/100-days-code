# Day 22 · this指针

## 今日目标
- 理解 this 指针是什么
- 会用 this 解决成员名与参数名冲突
- 理解链式调用 `return *this`

## 学习内容

### this 是什么

每个非静态成员函数都隐含一个隐藏参数 `this`，它指向**调用该函数的对象本身**：

```cpp
class Student {
    std::string name_;
public:
    void setName(const std::string& name) {
        this->name_ = name;     // this 是指向当前对象的指针
    }
};
```

编译器实际把 `s.setName("x")` 翻译成 `Student::setName(&s, "x")`，函数内 `this == &s`。

### 解决命名冲突

工程上成员变量常加 `_` 后缀，但偶尔参数和成员同名时用 this：

```cpp
class Point {
    int x, y;
public:
    Point(int x, int y) {
        this->x = x;
        this->y = y;
    }
};
```

### 链式调用：return *this

`this` 是指针，`*this` 就是对象本身。返回 `*this` 可以实现链式调用：

```cpp
class Builder {
    int value_ = 0;
public:
    Builder& add(int x)  { value_ += x; return *this; }
    Builder& mul(int x)  { value_ *= x; return *this; }
    int      result()    const { return value_; }
};

int main() {
    Builder b;
    int r = b.add(3).mul(4).add(1).result();   // (0+3)*4+1 = 13
}
```

像 `std::cout << a << b << c` 能连写，就是因为 `operator<<` 返回了 `*this`（ostream 引用）。

### 静态成员函数没有 this

静态成员函数不属于任何对象，所以没有 this，也不能访问普通成员。

### const 成员函数里的 this

const 成员函数里，`this` 的类型是 `const T*`，所以不能修改成员变量。

## 核心重点
- this 是指向当前对象的隐藏指针
- 解决参数与成员同名：`this->name = name;`
- 返回 `*this` 实现链式调用
- 静态成员函数没有 this
- const 成员函数里 this 是 const T*

## 练习/任务
1. 用 this 重写 Point 构造函数，参数名与成员名同名
2. 写一个 Builder 类，add/sub/mul 都返回 `*this`，实现链式计算
3. 给 Student 写链式 setter：`setAge(int)->setName(string)`，每个返回 `*this`
4. （选做）在静态成员函数里尝试使用 this，观察编译错误

## 打卡清单
- [ ] 已理解 this 指向当前对象
- [ ] 已会用 this 解决命名冲突
- [ ] 已会写 return *this 的链式调用
- [ ] 已知道静态函数没有 this
