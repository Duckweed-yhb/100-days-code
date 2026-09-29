# Day68 上下文切换

> 目标：搞懂 OS 怎么"暂停一个任务、换上另一个任务"——核心就是把 callee-saved 寄存器存进栈、再把下一个任务的寄存器装回来。这是多任务的心脏。

单核 CPU 同一时刻只能跑一个任务。要让多个任务"看起来同时在跑"，OS 就得不断地**保存当前任务的现场，恢复下一个任务的现场**。这就是**上下文切换（context switch）**。

## 现场是什么：要存哪些寄存器

Day62 讲过调用约定：函数调用时，caller-saved（`a/t/ra`）编译器已经帮你存了；真正跨任务切换时，我们只需要保存 **callee-saved 寄存器**——也就是 OS 主动切走别人时，按 ABI 必须替被切走者保管的那批。

rCore 的任务上下文只存 8 个：

| 寄存器 | 作用 |
|---|---|
| `ra` | 返回地址 |
| `sp` | 栈指针（任务自己的内核栈） |
| `s0`~`s11` | 12 个 callee-saved 保存寄存器 |

就这些。**注意不存 `a0~a7`、`t0~t6`**——因为它们是 caller-saved，下次被切回来时编译器会重新用，当前值没意义。

## TaskContext：一块存在栈上的结构

每个任务在内核栈上留一块**上下文（TaskContext）**，按上面顺序把寄存器压进去：

```rust
// 内核代码片段，需在 rCore 环境运行，不能在普通 cargo 项目直接跑
#[repr(C)]          // 让字段按 C 布局紧密排列，汇编才能按偏移存取
#[derive(Debug, Default, Clone, Copy)]
pub struct TaskContext {
    pub ra: usize,
    pub sp: usize,
    pub s: [usize; 12],   // s0~s11
}
```

`#[repr(C)]` 极其关键：Rust 默认字段顺序可能被重排，汇编按固定偏移 `0、8、16...` 存取时就会错位。加了它，布局就和 C 一样可预测。

## __switch：两个参数，一存一取

核心函数 `__switch(current_cx_ptr, next_cx_ptr)`：

```
__switch(&当前任务的TaskContext, &下一个任务的TaskContext)

干两件事：
  1. 把当前 8 个寄存器存进 current_cx_ptr 指向的内存
  2. 从 next_cx_ptr 指向的内存装回 8 个寄存器
     —— 装回 sp 后，"我们"就变成下一个任务了
```

汇编长这样（RISC-V 64 位，每个寄存器 8 字节）：

```asm
# 内核代码片段：__switch，需在 rCore 环境运行，不能在普通 cargo 项目直接跑
# a0 = &current_task_cx, a1 = &next_task_cx
__switch:
    # --- 把当前寄存器存进 current (a0) ---
    sd  ra, 0(a0)
    sd  sp, 8(a0)
    sd  s0, 16(a0)
    sd  s1, 24(a0)
    # ... s2~s10 ...
    sd  s11, 104(a0)

    # --- 从 next (a1) 装回寄存器 ---
    ld  ra, 0(a1)
    ld  sp, 8(a1)      # 关键！sp 一切，栈就换成别人的
    ld  s0, 16(a1)
    ld  s1, 24(a1)
    # ... s2~s10 ...
    ld  s11, 104(a1)

    ret                 # 跳回 next 任务当初存 ra 的地方
```

## 流程图：切换是怎么"穿越"的

```
任务 A 正在跑
   │  OS 决定切走，调 __switch(&A.cx, &B.cx)
   ▼
[A.cx] 写入 A 的 ra/sp/s0..s11   ← A 的现场被冻结
   │
   ▼  ld sp, 8(B.cx)  →  sp 变成 B 的栈
[B.cx] 读出 B 的 ra/sp/s0..s11
   │
   ▼  ret → 跳到 B 上次 __switch 里存的 ra
任务 B 接着上次的地方跑
```

最妙的一点：**`ret` 不是返回给调用者，而是跳到了"上一次 B 在 __switch 里存进去的 ra"**。对 B 来说，它感觉就是自己刚从 `__switch` 调用里正常返回了——完全不知道中间被换走过。

## 第一次创建任务：伪造一个现场

新任务从没跑过，栈上哪来的 TaskContext？答案是**手动伪造**：把它的 `ra` 填成"任务入口函数地址"，`sp` 填成它的栈顶。这样第一次 `__switch` 恢复它时，`ret` 就跳进入口函数了。这就是新任务"第一次起跑"的 trick。

## 今日小结

- 上下文切换 = 保存 callee-saved 寄存器到当前栈，从下一个任务栈装回
- rCore 只存 `ra/sp/s0~s11` 共 14 个值，`#[repr(C)]` 保证布局可被汇编按偏移存取
- `__switch(&cur_cx, &next_cx)`：存当前、装下一个，靠改 `sp` 完成"穿越"
- 新任务靠伪造一个 TaskContext（ra=入口函数）实现第一次起跑

## 动手任务

1. 在 rCore 里找到 `__switch`（`context.S` 或 `switch.S`），数一下它存/恢复了多少个 `sd`/`ld`。
2. 思考：为什么不保存 `a0`？如果硬存了会怎样？
3. 找到任务创建时伪造 TaskContext 的代码（`TaskControlBlock::new`），看 `ra` 被填成了哪个函数。

**配合阅读**：[rCore-Tutorial·第三章 多道程序与协作式调度（任务上下文切换）](https://rcore-os.cn/rCore-Tutorial-Book-v3/chapter3/index.html)
