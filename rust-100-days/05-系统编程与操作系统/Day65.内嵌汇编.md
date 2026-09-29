# Day65 内嵌汇编

> 目标：学会在 Rust 里用 `core::arch::asm!` 直接嵌 RISC-V 汇编，能写 wfi、读写 CSR、触发 ecall 这几个内核高频操作。

有些事 Rust 语法做不到：关中断、读某个 CSR、执行一条 CPU 才懂的特殊指令。这时候就得在 Rust 代码里直接插一行汇编——这就是**内嵌汇编（inline assembly）**。Rust 提供 `core::arch::asm!` 宏。

## 基本语法

```rust
// 内核代码片段，需在 rCore 环境运行，不能在普通 cargo 项目直接跑
#![feature(asm_const)] // 某些 nightly 特性
use core::arch::asm;

// 最简单：执行一条无参数汇编
unsafe {
    asm!("nop"); // 啥也不干，空指令
}
```

模板长这样：

```rust
unsafe {
    asm!(
        "模板字符串",        // 汇编指令，用 {0} {1} 占位
        in(reg) 输入值,       // 把值放进寄存器，供汇编用
        out(reg) 输出变量,    // 从寄存器读回结果
        options(nostack, preserves_flags), // 选项
    );
}
```

几个要点：

- **必须包在 `unsafe` 块里**：你在跟编译器保证这段汇编没破坏它的假设。
- **操作数约束**：`in(reg) x` 表示把 `x` 放进某个寄存器给汇编用；`out(reg) y` 表示执行后把寄存器写回 `y`。模板里用 `{0}`、`{1}` 按顺序引用。
- **`options`**：告诉编译器这段汇编的副作用，常见有 `nostack`（不碰栈）、`preserves_flags`（不改标志位）、`noreturn`（永不返回，比如死循环）。

## 例子一：wfi —— 让 CPU 睡一会

`wfi`（wait for interrupt）让 CPU 暂停，直到下一个中断到来。内核空闲时常这么用：

```rust
/// 让当前 hart 进入低功耗等待，直到中断到来
pub unsafe fn prepare_for_interrupt() {
    asm!("wfi", options(nostack));
}
```

## 例子二：读 / 写 CSR

读 CSR 用 `csrr`（csr read），写用 `csrw`（csr write）。比如读 `sstatus`：

```rust
/// 读取 sstatus 寄存器的值
pub fn read_sstatus() -> usize {
    let x: usize;
    unsafe {
        asm!("csrr {}, sstatus", out(reg) x);
    }
    x
}

/// 写 stvec：设置 trap 入口地址
pub unsafe fn write_stvec(addr: usize) {
    asm!("csrw stvec, {}", in(reg) addr);
}
```

`{}` 会被替换成编译器选好的寄存器名。rCore 里对每个常用 CSR 都包了一层这样的 unsafe 函数，业务代码就不用天天写汇编了。

## 例子三：ecall —— 陷入特权级

`ecall` 是"主动陷入"指令：用户态执行它会跳进 S mode，内核态执行它会跳进 M mode（调 SBI）。这就是系统调用的硬件触发点：

```rust
/// 发起一次 SBI 调用（在 S mode 执行 ecall 会陷入 M mode 的固件）
pub unsafe fn sbi_call(eid: usize, fid: usize, a0: usize, a1: usize) -> usize {
    let ret;
    asm!(
        "ecall",
        in("a7") eid,       // a7 放扩展 ID
        in("a6") fid,       // a6 放功能 ID
        in("a0") a0,
        in("a1") a1,
        out("a0") ret,      // 返回值在 a0
    );
    ret
}
```

这里用了**具名寄存器约束** `in("a7") eid`：直接指定把值放进 RISC-V 的 `a7`，因为 SBI/系统调用 ABI 规定了编号和参数必须在固定寄存器里。

## 什么时候才用内嵌汇编

记住原则：**能 Rust 就 Rust，只有碰硬件语义时才 asm!**。典型场景：

| 场景 | 指令 |
|---|---|
| 空闲等待 | `wfi` |
| 读写 CSR | `csrr`/`csrw` |
| 触发系统调用/SBI | `ecall` |
| 上下文切换保存恢复 | 整段手写汇编（Day68） |
| 内存屏障 | `fence` |

## 今日小结

- `core::arch::asm!` 在 Rust 里插汇编，必须放 `unsafe` 块
- 用 `in(reg)`/`out(reg)` 传值，`{0}` 占位；要对齐 ABI 时用 `in("a7")` 具名指定寄存器
- 内核高频三板斧：`wfi` 睡、`csrr/csrw` 读写 CSR、`ecall` 陷入
- 别滥用：上下文切换、特殊指令才用，业务逻辑交给 Rust

## 动手任务

1. 写一个 `read_sepc() -> usize` 函数，用 `csrr` 读 `sepc`。
2. 写一个函数 `set_user_interrupt_enabled()`，通过改写 `sstatus` 的 SPIE 位（先照 rCore 的实现抄）。
3. 在 rCore 源码里搜 `asm!`，列出你找到的所有用法，各属于上面哪一类。

**配合阅读**：[rCore-Tutorial·第一章 应用程序与基本执行环境（基于 SBI 完成输出和关机）](https://rcore-os.cn/rCore-Tutorial-Book-v3/chapter1/index.html)
