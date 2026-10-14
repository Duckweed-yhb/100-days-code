# Day 60 · 反射reflect入门

## 今日目标
- 理解反射是运行时检查类型
- 掌握 reflect.TypeOf / ValueOf
- 学会用反射读结构体字段

## 学习内容

### 反射是什么

反射让程序在运行时检查变量的具体类型与结构。JSON 序列化、ORM 都靠反射工作。

```go
import "reflect"

var x = 42
t := reflect.TypeOf(x)
v := reflect.ValueOf(x)
fmt.Println(t)       // int
fmt.Println(v)       // 42
```

### 区分 Type 与 Value

- `TypeOf`：类型信息（叫什么、有哪些字段）
- `ValueOf`：值信息（能读写）

### 反射读结构体字段

```go
type User struct {
    Name string `json:"name"`
    Age  int    `json:"age"`
}

u := User{Name: "Bob", Age: 20}
rv := reflect.ValueOf(u)
rt := rv.Type()

for i := 0; i < rv.NumField(); i++ {
    field := rt.Field(i)
    value := rv.Field(i)
    fmt.Println(field.Name, field.Tag.Get("json"), value)
}
```

这就是 JSON 序列化的原理：遍历结构体字段、读 tag、取值。

### 接口与反射

反射的入口是 `reflect.ValueOf(any)`——这就是为什么空接口是 Go 反射的桥梁。

## 核心重点
- reflect.TypeOf 看类型，reflect.ValueOf 看值
- 反射能在运行时遍历结构体字段
- 读 struct tag 用 `field.Tag.Get("json")`
- 反射绕过编译期检查，容易 panic
- 普通业务代码少用反射，框架代码才用

## 练习/任务
1. 用反射打印一个 int、string、struct 的类型
2. 遍历 User 结构体所有字段名、tag、值
3. 用反射修改一个结构体字段（提示：要传指针）
4. 故意对非结构体调用 NumField，观察 panic

## 今日刷题

### 1. LeetCode 169 多数元素（复习）
- 思路：投票。
- 代码提示：Boyer-Moore。
- Go 要点：计数。

### 2. LeetCode 1 两数之和（复习）
- 思路：map。
- 代码提示：查 target-x。
- Go 要点：map。

## 打卡清单
- [ ] 会用 TypeOf/ValueOf
- [ ] 会反射遍历结构体字段
- [ ] 会读 struct tag
- [ ] 复习两数之和与多数元素
