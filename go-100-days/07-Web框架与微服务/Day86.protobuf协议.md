# Day 86 · protobuf协议

## 今日目标
- 理解 Protobuf 序列化原理
- 掌握 message 定义与字段编号
- 知道 proto3 常用类型

## 学习内容

### Protobuf 是什么

Google 的二进制序列化格式，比 JSON 小、快、强类型。

### 为什么比 JSON 好

| 对比 | JSON | Protobuf |
|---|---|---|
| 大小 | 文本，大 | 二进制，小 3~10 倍 |
| 速度 | 慢 | 快 |
| 类型 | 弱 | 强 |
| Schema | 无 | .proto 定义 |

### 常用标量类型

| proto 类型 | Go 类型 |
|---|---|
| int32/int64 | int32/int64 |
| string | string |
| bool | bool |
| float/double | float32/float64 |
| repeated T | []T |

### message 定义

```proto
message User {
    int64 id = 1;
    string name = 2;
    string email = 3;
    repeated string tags = 4;
}
```

字段编号 1、2、3 是协议的一部分，**不能改**——改了就破坏兼容性。

### 嵌套与枚举

```proto
enum Status {
    UNKNOWN = 0;
    ACTIVE = 1;
    INACTIVE = 2;
}

message User {
    Status status = 5;
}
```

### 向后兼容

新增字段用新编号，老代码不认识就忽略——这就是 Protobuf 向后兼容的秘诀。

## 核心重点
- Protobuf 二进制序列化，小而快
- 字段编号是协议一部分，不能改
- repeated 表示数组
- enum 定义枚举
- 新增字段用新编号保持兼容

## 练习/任务
1. 定义一个 User message 含 id/name/email/tags
2. 加一个 Status 枚举
3. 生成 Go 代码并打印一个 User
4. 思考：为什么字段编号不能改

## 今日刷题

### 1. LeetCode 146 LRU 缓存（复习）
- 思路：map+链表。
- 代码提示：list.List。
- Go 要点：设计。

### 2. LeetCode 200 岛屿数量（复习）
- 思路：DFS。
- 代码提示：沉没。
- Go 要点：网格。

## 打卡清单
- [ ] 理解 Protobuf 优势
- [ ] 会写 message
- [ ] 理解字段编号
- [ ] 复习 LRU 与岛屿
