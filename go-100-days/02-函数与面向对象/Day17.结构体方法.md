# Day 17 · 结构体方法

## 今日目标
- 掌握 Go 方法的定义与接收者
- 理解值接收者与指针接收者的区别
- 学会为自定义类型写方法

## 学习内容

### 方法 = 函数 + 接收者

Go 没有 class，但可以给**任意类型**定义方法：

```go
type User struct {
    Name string
    Age  int
}

// 值接收者
func (u User) Greet() string {
    return "Hi, I'm " + u.Name
}

u := User{Name: "Bob", Age: 20}
fmt.Println(u.Greet())
```

`(u User)` 叫接收者，相当于其他语言里的 `this`。

### 值接收者 vs 指针接收者

```go
// 值接收者：操作的是副本
func (u User) HaveBirthdayBad() {
    u.Age++
}

// 指针接收者：能改原对象
func (u *User) HaveBirthday() {
    u.Age++
}

u := User{Age: 20}
u.HaveBirthdayBad()   // 无效，u.Age 还是 20
u.HaveBirthday()      // 有效，u.Age 变成 21
```

### 选型规则

| 情况 | 接收者 |
|---|---|
| 需要修改原对象 | 指针接收者 |
| 结构体很大，避免拷贝 | 指针接收者 |
| 小对象、只读操作 | 值接收者 |
| **同一类型的方法保持一致**，不要混用 | 选一种 |

### 为非结构体类型写方法

```go
type Celsius float64

func (c Celsius) Fahrenheit() float64 {
    return float64(c)*9/5 + 32
}
```

注意：只能为**同一个包里定义的类型**定义方法，不能给 int、string 等外来类型加方法。

## 核心重点
- 方法就是带接收者的函数
- 值接收者读副本，指针接收者改原对象
- 需要修改状态或结构体较大时用指针接收者
- 同一类型的方法接收者风格要统一
- 只能为本包自定义类型加方法

## 练习/任务
1. 给 `Book` 结构体写方法 `Describe() string`
2. 给 `Counter` 结构体（含 int 字段）写 `Inc()` 指针方法
3. 写一个 `Celsius` 类型，加 `Fahrenheit()` 方法
4. 故意用值接收者修改字段，观察为什么不生效

## 打卡清单
- [ ] 会为结构体定义方法
- [ ] 分得清值接收者与指针接收者
- [ ] 知道什么时候必须用指针接收者
- [ ] 会为自定义基本类型加方法
