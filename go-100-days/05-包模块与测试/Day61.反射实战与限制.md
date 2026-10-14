# Day 61 · 反射实战与限制

## 今日目标
- 学会用反射修改值、调用方法
- 理解反射的性能代价与安全风险
- 知道什么时候该用反射

## 学习内容

### 反射修改值

要修改必须传指针：

```go
u := User{Name: "Bob"}
rv := reflect.ValueOf(&u)      // 传指针
rv.Elem().FieldByName("Name").SetString("Alice")
fmt.Println(u.Name)            // Alice
```

`Elem()` 解引用指针，FieldByName 取字段。

### 反射调用方法

```go
rv := reflect.ValueOf(u)
method := rv.MethodByName("Greet")
results := method.Call(nil)
fmt.Println(results[0])
```

### 反射的代价

| 问题 | 说明 |
|---|---|
| 性能 | 比直接调用慢几倍到几十倍 |
| 失去类型检查 | 编译期不报错，运行时 panic |
| 代码难读 | 反射代码可读性差 |
| 重构脆弱 | 字段改名编译期发现不了 |

### 什么时候用反射

- 写框架（JSON、ORM、依赖注入）
- 需要根据字段名动态处理
- 配置序列化

普通业务代码**不要用反射**，直接写明确的类型代码。

### reflect.DeepEqual

```go
reflect.DeepEqual([]int{1,2}, []int{1,2})   // true
```

比较复杂结构相等，但性能差，测试里可接受。

## 核心重点
- 修改值要传指针并 Elem()
- MethodByName + Call 动态调用
- 反射慢、不安全、难重构
- 框架代码才用，业务代码别用
- DeepEqual 测试方便但慢

## 练习/任务
1. 用反射把 User.Name 改成 "Alice"
2. 用反射调用 User 的 Greet 方法
3. 用 reflect.DeepEqual 比较两个切片
4. 写一段代码：根据字段名字符串动态设置结构体字段

## 今日刷题

### 1. LeetCode 20 有效的括号（复习）
- 思路：栈。
- 代码提示：map 配对。
- Go 要点：栈。

### 2. LeetCode 21 合并两个有序链表（复习）
- 思路：哑节点。
- 代码提示：谁小接谁。
- Go 要点：链表。

## 打卡清单
- [ ] 会用反射修改字段
- [ ] 会反射调用方法
- [ ] 理解反射代价
- [ ] 复习括号与链表
