# Day69 中断与异常

> 目标：彻底搞懂 trap（陷入）的来龙去脉：出事时 CPU 自动做了什么、内核入口怎么保存全部寄存器、怎么分发原因、处理完怎么回去。

**Trap（陷入）**是操作系统和硬件打交道的总入口。它分三类：

| 类型 | 触发者 | 例子 |
|---|---|---|
| 异常（exception） | 当前指令自己出错 | 缺页、非法指令、除数为零 |
| 陷入（system call trap） | 主动 `ecall` | 用户态请求内核服务 |
| 中断（interrupt） | 外部设备/时钟 | 定时器到了、键盘按下 |

不管哪种，硬件动作都一样：**填好几个 CSR，跳 `stvec` 指向的入口。**

## 出事瞬间，CPU 自动做了什么

以用户态执行一条指令触发 trap 为例，RISC-V S mode 硬件自动完成：

```
1. sepc  ← 出事先那条指令的地址   (处理完从这继续)
2. scause← 原因编号               (缺页? ecall? 时钟中断?)
3. stval ← 附加信息               (比如触发缺页的虚拟地址)
4. sstatus.SPP ← 之前的特权级     (记下我是从 U 还是 S 陷进来的)
5. sstatus.SPIE ← 之前中断是否开
6. 关中断 (SIE=0)
7. pc    ← stvec 指向的入口
```

就这七步，全自动。内核什么都不用教，硬件干完直接跳到 `stvec`。

## 五个 CSR 速查

| CSR | 中文 | 里面是啥 |
|---|---|---|
| `stvec` | 中断向量基址 | trap 入口地址（内核启动时设） |
| `sepc` | 异常程序计数器 | 出事指令地址 |
| `scause` | 异常原因 | 一个编号，区分缺页/ecall/时钟 |
| `stval` | 异常附加值 | 缺页时是出错的虚地址 |
| `sstatus` | 状态寄存器 | 之前特权级、中断开关等 |

`scause` 的最高位是 interrupt 标志（1=外部中断，0=异常/ecall），低位是具体编号。内核读它就知道"发生了什么"。

## trap 入口：先存全部寄存器

`stvec` 指向的入口是**一段汇编**。因为接下来要调 Rust 处理函数，得先把所有通用寄存器压进内核栈——否则 Rust 函数一调用就把寄存器踩烂了：

```asm
# 内核代码片段：trap 入口，需在 rCore 环境运行，不能在普通 cargo 项目直接跑
    .globl __alltraps
__alltraps:
    # 把 32 个通用寄存器全部压到内核栈，构造一个 TrapContext
    # (x2/sp 也压进去，因为接下来要切到别的栈)
    sd  x1,  8*0(sp)
    sd  x2,  8*1(sp)
    # ... 一直到 x31 ...
    sd  x31, 8*31(sp)

    # sp 作为参数(a0)传给 Rust 处理函数
    mv  a0, sp
    call trap_handler        # 调 Rust！

    # 处理完，从内核栈恢复全部寄存器
__alltraps_return:
    ld  x1,  8*0(sp)
    # ... ld x2..x31 ...
    ld  x31, 8*31(sp)
    sret                     # 回到 sepc 指向的地址
```

注意和 Day68 的区别：

- **上下文切换**（`__switch`）只存 callee-saved 的 8 个，因为是"主动切"。
- **trap 入口**（`__alltraps`）要存**全部 32 个**，因为是硬件突然打断，被打断者正在用所有寄存器。

## Rust 侧分发：trap_handler

拿到栈上的 `TrapContext` 后，Rust 根据 `scause` 分发：

```rust
// 内核代码片段，需在 rCore 环境运行，不能在普通 cargo 项目直接跑
pub fn trap_handler(cx: &mut TrapContext) -> &mut TrapContext {
    let scause = scause::read();
    let stval = stval::read();
    match scause.cause() {
        // 用户态主动 ecall：系统调用
        Trap::Exception(Exception::UserEnvCall) => {
            cx.sepc += 4;            // 跳过 ecall 指令本身
            cx.x[10] = syscall(cx.x[17], [cx.x[10], cx.x[11], cx.x[12]]) as usize;
            // a0(x10) 放返回值；a7(x17) 是 syscall 编号
        }
        // 缺页异常
        Trap::Exception(Exception::StorePageFault) |
        Trap::Exception(Exception::LoadPageFault) => {
            panic!("page fault at {:#x}", stval);
        }
        // 时钟中断：抢占式调度靠它
        Trap::Interrupt(Interrupt::SupervisorTimer) => {
            set_next_trigger();      // 设下一次时钟
            scheduler::schedule();   // 切到下一个任务
        }
        _ => panic!("unhandled trap {:?}", scause),
    }
    cx
}
```

## 用户态 vs 内核态 trap

- **从用户态陷进来**：处理完 `sret` 回用户态，栈是用户的还是内核的？——我们压到的是**当前任务的内核栈**，返回时切回用户栈。
- **在内核态又出 trap**（比如内核自己缺页）：rCore 教学版里通常直接 panic，因为内核不该自己犯这种错。

## 回到用户态

最后 `sret` 这条指令：硬件自动从 `sstatus.SPP` 知道该回 U 还是 S，并跳到 `sepc`。于是被中断的用户程序就像什么都没发生一样继续跑。

## 今日小结

- trap 三类：异常、ecall 陷入、外部中断；硬件统一动作是填 CSR 再跳 `stvec`
- 五个 CSR：`stvec`(入口)/`sepc`(返回地址)/`scause`(原因)/`stval`(附加)/`sstatus`(状态)
- `__alltraps` 存全部 32 个寄存器再调 Rust；`trap_handler` 按 `scause` 分发
- ecall 处理：`sepc += 4` 跳过指令，`a7` 是编号、`a0` 是返回值；时钟中断负责抢占调度

## 动手任务

1. 在 rCore 找 `__alltraps` 和 `trap_handler`，对照画出"入口→存寄存器→Rust→恢复→sret"的箭头。
2. 查 RISC-V 手册：`scause` 里 "U-mode ecall" 的编号是多少？
3. 故意在用户程序里写一条非法指令，观察内核 `panic` 时打印的 `scause` 和 `stval`。

**配合阅读**：[rCore-Tutorial·第三章 多道程序与协作式调度（Trap 处理）](https://rcore-os.cn/rCore-Tutorial-Book-v3/chapter3/index.html)
