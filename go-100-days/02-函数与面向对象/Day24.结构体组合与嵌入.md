# Day 24 · 结构体组合与嵌入

## 今日目标
- 理解 Go "组合优于继承"
- 掌握结构体嵌入与字段提升
- 学会用接口嵌入扩展接口

## 学习内容

### Go 没有继承

Go 用**组合**代替类继承。结构体里嵌入另一个类型，就拥有了它的字段和方法。

```go
type Animal struct {
    Name string
}

func (a Animal) Eat() {
    fmt.Println(a.Name, "吃东西")
}

type Dog struct {
    Animal          // 嵌入 Animal（匿名字段）
    Breed string
}

d := Dog{Animal: Animal{Name: "旺财"}, Breed: "金毛"}
d.Eat()              // 方法被"提升"上来，直接调用
fmt.Println(d.Name)  // 字段也提升
```

### 字段提升

`d.Name` 等价于 `d.Animal.Name`。嵌入让代码更简洁，但本质还是组合。

### 方法重写

```go
func (d Dog) Eat() {
    fmt.Println(d.Name, "啃骨头")
}
```

Dog 自己的 `Eat` 会遮蔽嵌入的 Animal.Eat，这是 Go 版的"重写"。

### 接口嵌入

```go
type Reader interface { Read(p []byte) (int, error) }
type Writer interface { Write(p []byte) (int, error) }
type ReadWriter interface {     // 组合两个接口
    Reader
    Writer
}
```

标准库 `io.ReadWriter` 就是这么来的。

### 什么时候用嵌入

- 复用字段/方法：嵌入结构体
- 复用接口契约：嵌入接口
- 不要为了"像面向对象"而强行套，组合要表达"has-a"关系

## 核心重点
- Go 没有类继承，用结构体嵌入实现组合
- 嵌入类型的字段和方法被提升到外层
- 外层同名方法会遮蔽嵌入的
- 接口也能嵌入，组合出更大接口
- 优先组合而非继承

## 练习/任务
1. 定义 `Employee` 嵌入 `Person`，写出字段提升效果
2. 给 Dog 写自己的 Eat 方法，验证遮蔽了 Animal.Eat
3. 模仿 io.ReadWriter，自己组合 Reader 和 Closer 接口
4. 写一个 `VIPUser` 嵌入 `User`，加 `Level` 字段与方法

## 打卡清单
- [ ] 会用结构体嵌入实现组合
- [ ] 理解字段/方法提升
- [ ] 会写同名方法遮蔽
- [ ] 会嵌入接口
