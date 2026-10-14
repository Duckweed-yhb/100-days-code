# Day 16 · 结构体struct

## 今日目标
- 掌握结构体定义与实例化
- 理解字段、匿名字段、结构体指针
- 学会用结构体组织复杂数据

## 学习内容

### 定义结构体

```go
type User struct {
    Name string
    Age  int
}
```

### 创建实例

```go
u1 := User{Name: "Bob", Age: 20}     // 字段名赋值，推荐
u2 := User{"Bob", 20}                // 按位置赋值（少用，字段顺序变就崩）
u3 := new(User)                      // 返回 *User，字段零值
u3.Name = "Cat"

var u4 User                          // 零值结构体，可直接用
```

### 访问字段

```go
u1.Name = "Alice"
fmt.Println(u1.Age)
```

### 结构体是值类型

赋值和传参会拷贝整个结构体：

```go
u5 := u1
u5.Name = "Changed"
fmt.Println(u1.Name)   // 还是 Alice，不受影响
```

大结构体频繁拷贝影响性能，所以函数参数常用 `*User`。

### 结构体指针

```go
p := &User{Name: "Bob", Age: 20}
p.Age = 21          // Go 自动解引用，不用写 (*p).Age
```

### 匿名字段（嵌入，预告）

```go
type Point struct {
    X, Y int
}
type Circle struct {
    Point            // 匿名字段，嵌入
    Radius int
}
c := Circle{Point: Point{1, 2}, Radius: 5}
fmt.Println(c.X)    // 直接访问嵌入字段
```

Day24 会专门讲组合与嵌入。

## 核心重点
- `type 名字 struct { 字段 类型 }` 定义结构体
- 实例化用字段名赋值最安全
- 结构体是值类型，赋值会拷贝
- 大对象用指针 `*User` 传递
- 匿名字段用于组合，不是继承

## 练习/任务
1. 定义一个 `Book` 结构体：Title、Author、Price
2. 创建三本书放进切片，遍历打印
3. 写一个 `PrintUser(u User)` 和 `PrintUserPtr(u *User)`，观察拷贝与指针差别
4. 给 `User` 加一个 `Addr` 结构体类型字段（嵌套结构体）

## 打卡清单
- [ ] 会定义、实例化、访问结构体字段
- [ ] 理解结构体是值类型
- [ ] 会用结构体指针
- [ ] 知道字段名赋值优于位置赋值
