# Day 86 · 工厂模式

## 今日目标
- 理解工厂模式解决什么问题
- 学会用简单工厂创建对象
- 与多态结合

## 学习内容

### 问题：new 太多处

如果代码里到处写 `new Circle(...)`、`new Rectangle(...)`，将来加新形状要改一堆地方。工厂模式把"创建对象"集中到一个地方。

### 简单工厂

```cpp
class Shape {
public:
    virtual double area() const = 0;
    virtual ~Shape() = default;
};

class Circle : public Shape { /* ... */ };
class Rectangle : public Shape { /* ... */ };

class ShapeFactory {
public:
    static std::unique_ptr<Shape> create(const std::string& type) {
        if (type == "circle") return std::make_unique<Circle>();
        if (type == "rect")   return std::make_unique<Rectangle>();
        return nullptr;
    }
};

// 使用
auto s = ShapeFactory::create("circle");
```

### 好处

- 创建逻辑集中；
- 加新形状只改工厂一处；
- 调用者不需要知道具体类。

### 与依赖注入配合

工厂创建对象后，外部拿到的是 `Shape*`，不关心具体类型，符合"面向接口编程"。

### 工厂方法与抽象工厂

GoF 里还有更复杂的"工厂方法"和"抽象工厂"，初学阶段简单工厂够用。理解思想即可：**把创建对象的代码集中起来**。

## 核心重点
- 工厂模式集中对象创建逻辑
- 静态方法返回基类指针/智能指针
- 加新类型只改工厂
- 调用者面向接口编程
- 简单工厂够用，复杂模式以后再学

## 练习/任务
1. 把 Day37 的 Shape 体系加一个 ShapeFactory
2. 写一个"日志器工厂"：根据字符串创建 FileLogger 或 ConsoleLogger
3. 在 main 中用工厂创建对象，不直接 new
4. （选做）加一个 Triangle 形状，看只改工厂一处够不够

## 今日刷题

- **LeetCode 200. 岛屿数量**。
- **LeetCode 133. 克隆图**。
- **LeetCode 207. 课程表**。

## 打卡清单
- [ ] 已会写简单工厂
- [ ] 已理解工厂集中创建逻辑
- [ ] 已用智能指针返回对象
- [ ] 已完成至少一道图论题
