# Day 18 · 接口interface

## 今日目标
- 理解 Go 接口"隐式实现"的哲学
- 掌握接口定义与使用
- 学会面向接口编程

## 学习内容

### 接口是什么

接口定义一组方法签名。**只要一个类型实现了接口的所有方法，它就实现了这个接口**——不需要 `implements` 关键字。这叫结构化类型。

```go
type Speaker interface {
    Speak() string
}

type Dog struct{}
func (d Dog) Speak() string { return "汪" }

type Person struct{ Name string }
func (p Person) Speak() string { return "我是" + p.Name }
```

Dog 和 Person 都自动满足 `Speaker`。

### 使用接口

```go
func makeSpeak(s Speaker) {
    fmt.Println(s.Speak())
}

makeSpeak(Dog{})
makeSpeak(Person{Name: "Bob"})
```

同一个函数，传入不同类型，表现不同——这就是多态。

### 空接口 interface{}

`interface{}`（Go 1.18 后也写作 `any`）能装任意类型：

```go
var anything any
anything = 1
anything = "hello"
anything = User{Name: "Bob"}
```

打印日志、通用容器里常用。

### 接口值内部结构

接口值由 (类型, 值) 两部分组成。**一个含 nil 值的接口本身不等于 nil**，这是常见坑，Day23 错误包装时会再碰。

### 小接口原则

Go 推崇小接口：`io.Reader`、`io.Writer` 都只有一个方法。定义接口时不要贪多，调用方按需组合。

## 核心重点
- 接口是方法签名集合，实现是隐式的
- 不需要声明"我实现了某接口"，有方法就算
- `any` = `interface{}`，可装任意值
- 面向接口编程，函数参数用接口而非具体类型
- 接口要小而专

## 练习/任务
1. 定义 `Stringer` 接口（String() string），让 User 实现它
2. 写一个 `Play(s Speaker)` 函数，传入 Dog、Cat、Person 看多态
3. 写一个 `PrintAny(v any)` 用 fmt 打印任意值
4. 定义一个 `Shape` 接口含 `Area() float64`，让 Circle、Rectangle 实现

## 打卡清单
- [ ] 理解隐式实现，不用 implements
- [ ] 会定义接口并写面向接口函数
- [ ] 知道 any 是空接口
- [ ] 体会多态：同一函数不同行为
