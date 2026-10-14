# Day 63 · 测试与泛型阶段练习

## 今日目标
- 综合运用模块管理、测试、泛型、反射写一个小库
- 给库配上完整测试
- 完成阶段五算法练习

## 学习内容

### 项目：一个泛型集合库

写一个泛型 `Set[T comparable]`，支持 Add/Has/Remove，并配上完整测试。

```go
// set.go
package set

// Set 是一个基于 map 的泛型集合。
type Set[T comparable] struct {
    data map[T]struct{}
}

// New 创建一个空集合。
func New[T comparable]() *Set[T] {
    return &Set[T]{data: make(map[T]struct{})}
}

// Add 加入一个元素。
func (s *Set[T]) Add(v T) {
    s.data[v] = struct{}{}
}

// Has 判断元素是否存在。
func (s *Set[T]) Has(v T) bool {
    _, ok := s.data[v]
    return ok
}

// Remove 删除元素。
func (s *Set[T]) Remove(v T) {
    delete(s.data, v)
}

// Size 返回元素个数。
func (s *Set[T]) Size() int {
    return len(s.data)
}
```

```go
// set_test.go
package set

import "testing"

func TestSet(t *testing.T) {
    s := New[int]()
    s.Add(1)
    s.Add(2)
    s.Add(1)

    if s.Size() != 2 {
        t.Errorf("Size = %d, want 2", s.Size())
    }
    if !s.Has(2) {
        t.Error("应有 2")
    }
    s.Remove(2)
    if s.Has(2) {
        t.Error("删除后不应有 2")
    }
}
```

要点：
- 泛型 `[T comparable]` 支持任意可比较类型
- map[T]struct{} 实现 set（空 struct 不占空间）
- 配上单元测试

## 核心重点
- 泛型库 + 完整测试是标准做法
- set 用 map[T]struct{} 实现
- 导出类型与方法都要注释
- 测试覆盖添加、存在、删除

## 练习/任务
1. 跑通上面 Set 并写测试
2. 加一个 Union(other *Set[T]) 方法求并集
3. 写表驱动测试覆盖多种类型（int、string）
4. （选做）给 Set 写基准测试测 Add 性能

## 今日刷题

### 1. LeetCode 141 环形链表（复习）
- 思路：快慢指针。
- 代码提示：相遇。
- Go 要点：链表。

### 2. LeetCode 104 二叉树最大深度（复习）
- 思路：递归。
- 代码提示：1+max。
- Go 要点：树。

## 打卡清单
- [ ] 完成泛型 Set 库
- [ ] 写好单元测试
- [ ] 跑完并通过 go test
- [ ] 阶段五所有概念亲手写过
