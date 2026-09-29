# Day67 启动流程

> 目标：说清楚 CPU 上电后第一条指令是什么、`_start` 干哪三件事（清 bss、设栈、跳 rust_main），以及控制权怎么从 OpenSBI 交到我们手里。

今天把"内核从被加载到第一个 Rust 函数跑起来"这条最短链路走一遍。这是整个 OS 的起跑点。

## 谁先跑：OpenSBI 把内核拉起来

回顾 Day61 的特权级。QEMU 开机先跑 **OpenSBI 固件**（M mode）：初始化一下硬件，然后跳到链接脚本里 `ENTRY(_start)` 指向的地址——也就是我们内核的第一条指令。

```
QEMU 上电
  │
  ▼
OpenSBI (M mode) 初始化硬件
  │  跳转到 0x80000000 (内核 _start)
  ▼
_start (S mode，我们写的汇编)
  │  三件套
  ▼
rust_main() (我们写的 Rust)
```

注意：`_start` 跑的时候，**栈还没设、bss 还没清、CPU 状态是一片空白**。所以它必须用汇编写，不能直接调 Rust。

## _start 必做三件事

```asm
# 内核代码片段：_start 入口汇编，需在 rCore 环境运行，不能在普通 cargo 项目直接跑
    .section .text.entry      # 链接脚本里保证这段排在最前面
    .globl _start
_start:
    # ① 设栈指针：sp 指向脚本里定义的 boot_stack_top
    la  sp, boot_stack_top

    # ② 清零 .bss 段：从 __bss_start 循环写到 __bss_end
    la  t0, __bss_start
    la  t1, __bss_end
1:
    bge t0, t1, 2f            # 到 end 就跳走
    sd  zero, 0(t0)           # 写 8 字节零
    addi t0, t0, 8
    j   1b
2:

    # ③ 一切就绪，跳 Rust 主函数
    call rust_main

    # rust_main 正常返回说明内核跑完了，调用 SBI 关机
    call sbi_shutdown
1:  j   1b                    # 兜底死循环
```

三件事逐个解释：

1. **设栈 `sp`**：在任何函数调用之前，必须有栈。`la sp, boot_stack_top` 把栈指针指到链接脚本留好的栈顶。没有栈，`call rust_main` 连返回地址都没地方存。
2. **清 `.bss`**：Day66 说过 `.bss` 是零初始化区，文件里不存零，靠启动时手动清零。不然全局变量一开始是随机垃圾值，`static mut` 全乱套。
3. **跳 `rust_main`**：前两步做完，C/Rust runtime 的最基本假设成立了，才敢 `call` 到 Rust 写的主函数。

## 为什么必须先设栈再 call

想深一层：`call rust_main` 这条指令本身会把返回地址压进 `ra`，而 `rust_main` 里如果再调用任何函数，就会用 `sp` 开栈帧。**如果 sp 还是开机时的乱值，第一次函数调用就炸。** 所以"设栈"必须是 `_start` 的第一步。

## 从 rust_main 开始干什么

`rust_main` 是我们写的 Rust 入口，rCore 里它按顺序做：

```rust
// 内核代码片段，需在 rCore 环境运行，不能在普通 cargo 项目直接跑
#[no_mangle]          // 不改编译后的符号名，让汇编能 call 到
pub fn rust_main() -> ! {
    // 1. 初始化串口输出，这样才能 println!
    crate::console::init();
    println!("Hello, rCore!");

    // 2. (后续章节才会加) 物理内存管理、页表、任务调度...

    // 3. 没有用户进程可跑时，就 wfi 睡等中断
    unsafe {
        loop {
            asm!("wfi");
        }
    }
}
```

`#[no_mangle]` 很关键：Rust 默认会把函数名 mangle（改编译），汇编里 `call rust_main` 就找不到符号了。加了它，导出的符号就叫 `rust_main`。

## SBI：内核的"BIOS"

内核想关机、想输出字符，但在 S mode 不能直接碰 M mode 的硬件。于是又一次 `ecall` 陷入 M mode，让 OpenSBI 固件代劳——这套约定叫 **SBI（Supervisor Binary Interface）**。rCore 把它包成 `sbi_call`：输出字符、关机都是一次 SBI 调用。

```
内核(S mode) --ecall--> OpenSBI(M mode) 代操作 --返回--> 内核
```

## 今日小结

- 上电先跑 OpenSBI(M mode)，再跳内核 `_start`(S mode)
- `_start` 三步：设 `sp` 栈顶 → 清零 `.bss` → `call rust_main`
- `#[no_mangle]` 保证 `rust_main` 符号不被改编，汇编才能 call 到
- 内核关不了机、不能直接碰底层硬件，靠 `ecall` 调 SBI 让固件代劳

## 动手任务

1. 在 rCore 源码找到 `_start`（通常在 `os/src/boot/` 或 `entry.asm`），对照上面三件事注释每行。
2. 故意不清 bss，在 Rust 里放一个 `static mut CNT: usize = 0;` 然后自增，观察现象。
3. 用 QEMU + GDB 断点打在 `_start` 和 `rust_main`，单步走过启动流程。

**配合阅读**：[rCore-Tutorial·第一章 应用程序与基本执行环境（内核第一条指令）](https://rcore-os.cn/rCore-Tutorial-Book-v3/chapter1/index.html)
