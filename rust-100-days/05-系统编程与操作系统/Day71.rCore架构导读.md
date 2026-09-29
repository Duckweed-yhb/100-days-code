# Day71 rCore 架构导读

> 目标：在继续抠每个子系统之前，先鸟瞰 rCore 整个内核的目录结构和分层，知道"一个系统调用从用户态走到内核"经过了哪几层。

前面十天我们把"裸铁"上能跑的最小东西拼出来了。从今天开始，正式进入 rCore 这个教学内核本身。先别急着读代码，先搞清楚地图。

## rCore 是什么

rCore-Tutorial 是用 Rust 写、跑在 RISC-V 上的**教学级类 Unix 内核**。它的特点是**逐章叠加功能**：每一章在前面基础上加一个子系统，最终长成一个能跑用户程序、有文件系统、多线程的小 OS。

```
ch1 最小内核(能输出/关机)
ch2 能批处理跑一个用户程序
ch3 多道程序 + 协作式调度 + trap
ch4 地址空间(页表)
ch5 进程管理 + 系统调用(fork/exec/waitpid)
ch6 文件系统 easy-fs
ch7 管道 IPC
ch8 线程 + 同步互斥
```

## 内核目录分层

rCore 的 `os/src/` 大致这样分（越往下越靠硬件）：

```
os/src/
├── main.rs        入口 rust_main
├── config.rs      常量：内核栈大小、物理内存末地址、页大小
├── init.rs        启动后各子系统初始化顺序
├── console.rs     串口输出封装
├── trap/          异常/中断/系统调用陷入与返回
│   ├── context.rs   __alltraps、TrapContext
│   └── handler.rs   trap_handler 分发
├── task/          进程/任务
│   ├── task.rs      TaskControlBlock
│   ├── processor.rs 当前任务/调度
│   └── switch.S     __switch 汇编
├── mm/            内存管理
│   ├── frame.rs     物理页帧分配器
│   ├── page_table.rs 页表操作
│   └── memory_set.rs 地址空间/内存集合
├── syscall/       系统调用实现
└── fs/            文件系统(ch6 后)
```

记住分层关系：

```
        用户程序 (U mode)
   ──────────── ecall / 中断 ────────────
        syscall/      ← 系统调用接口
        task/         ← 进程调度
        trap/         ← 陷入处理
        mm/           ← 内存管理
        fs/           ← 文件系统
   ──────────── 硬件 (QEMU/RISC-V) ────────────
```

## 一次系统调用走过的路

以用户程序 `write(1, "hi", 2)` 为例，从头到尾：

```
1. 用户库函数 write() 把参数塞 a0..a2, 编号塞 a7, 执行 ecall
2. 硬件自动填 sepc/scause/stval, 跳 stvec
3. __alltraps (汇编) 把 32 个寄存器压内核栈
4. call trap_handler
5. trap_handler 发现是 UserEnvCall, 调 syscall()
6. syscall() 根据 a7 编号分发到 sys_write
7. sys_write 调 fs::write → 把字符写进块设备/串口
8. 返回值放 a0, trap_handler 返回
9. __alltraps_return 恢复 32 个寄存器
10. sret 回到用户程序 sepc 处
```

这张图就是后面 Day72~Day78 要逐段填肉的骨架。

## 设计上的几个关键词

| 概念 | 大白话 | 对应模块 |
|---|---|---|
| 地址空间 MemorySet | 一个进程的"虚拟内存全景" | mm/memory_set.rs |
| TaskControlBlock (TCB) | 一个进程的全部档案 | task/task.rs |
| TaskManager | 所有就绪任务的总队列 | task/processor.rs |
| TrapContext | 一次陷入时保存的寄存器现场 | trap/context.rs |

## 今日小结

- rCore 按章叠加：ch1 最小 → ch8 多线程文件系统，每章加一个子系统
- 目录分层：trap 管陷入、task 管调度、mm 管内存、syscall 管接口、fs 管文件
- 一次 ecall：硬件填 CSR → __alltraps 存寄存器 → Rust 分发 → 处理 → 恢复 → sret
- 先看地图再读代码，别一上来扎进某个 `.rs` 出不来

## 动手任务

1. clone `LearningOS/rCore-Tutorial-Code-2024S`，切到 ch1，把 `os/src/` 目录树打印出来。
2. 从 `rust_main` 开始读，列出它依次调用了哪些 `xxx::init()`。
3. 对照上面"系统调用走过的路"，预测 sys_write 最终会调到哪个模块。

**配合阅读**：[rCore-Tutorial·第一章 应用程序与基本执行环境（本章代码导读）](https://rcore-os.cn/rCore-Tutorial-Book-v3/chapter1/index.html)
