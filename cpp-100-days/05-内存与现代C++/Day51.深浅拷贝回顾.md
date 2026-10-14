# Day 51 · 深浅拷贝回顾

## 今日目标
- 系统回顾浅拷贝与深拷贝
- 理解默认成员函数的生成规则
- 为智能指针与移动语义做铺垫

## 学习内容

### 回顾：默认拷贝行为

如果你不写析构、拷贝构造、拷贝赋值，编译器会帮你生成默认版本：**逐成员拷贝**。

```cpp
class String {
    char* data_;
public:
    String(const char* s) {
        data_ = new char[strlen(s) + 1];
        strcpy(data_, s);
    }
    ~String() { delete[] data_; }
};
```

这个类如果让编译器默认生成拷贝构造，两个 String 对象会共享同一块 `data_`，析构时 double free。

### 深拷贝写法

```cpp
String(const String& o) {
    data_ = new char[strlen(o.data_) + 1];
    strcpy(data_, o.data_);
}

String& operator=(const String& o) {
    if (this == &o) return *this;
    delete[] data_;
    data_ = new char[strlen(o.data_) + 1];
    strcpy(data_, o.data_);
    return *this;
}
```

### 什么时候不需要深拷贝

- 类里没有 `new` 出来的资源；
- 类的成员都是值类型或会自己管理内存（如 `std::string`、`std::vector`）。

经验：**尽量用标准库成员（string/vector）代替裸指针，就能免写深拷贝**。

### = default 与 = delete

```cpp
class String {
public:
    String(const String&) = default;             // 让编译器生成
    String& operator=(const String&) = default;
    // 或
    String(const String&) = delete;               // 禁止拷贝
};
```

### 三/五法则回顾

- 只要写了析构、拷贝构造、拷贝赋值其中一个，就要考虑写全部；
- C++11 再加移动构造、移动赋值，合称五法则。

明天开始讲智能指针，它让你再也不用手写这些。

## 核心重点
- 有裸指针资源的类必须自己写拷贝构造和赋值
- 优先用 string/vector 等 RAII 成员，免写深拷贝
- = default / = delete 显式控制
- 三/五法则
- 下一阶段主角：智能指针

## 练习/任务
1. 写一个 MyString 类，自己管理 char*，写全三/五法则
2. 用 vector<char> 重写 MyString，对比代码量
3. 故意不写拷贝构造，制造 double free，再修复
4. （选做）用 = delete 写一个不可拷贝类

## 今日刷题

- **LeetCode 146. LRU 缓存**：哈希表 + 双向链表。
- **LeetCode 460. LFU 缓存**：哈希表 + 平衡桶（进阶）。
- **LeetCode 42. 接雨水**：双指针或单调栈。

## 打卡清单
- [ ] 已能写深拷贝
- [ ] 已理解三/五法则
- [ ] 已会用 = default / = delete
- [ ] 已完成至少一道 Hard 刷题
