# Day80 Hypervisor 入门

> 目标：回顾宏内核/微内核，理解虚拟化到底在做什么，搞懂 VMM/guest/陷入模拟 这套术语，以及 RISC-V H 扩展怎么用 hgatp 和两阶段地址翻译支撑虚拟机。

这是系统编程阶段的收官篇。前面我们自己写了一个内核（rCore）。今天往上再走一层：**让一个内核像应用一样跑在另一个内核上面**——这就是虚拟化，管理它的软件叫 **Hypervisor（虚拟机监视器）**。

## 先回顾：宏内核 vs 微内核

| 架构 | 特征 | 代表 |
|---|---|---|
| 宏内核（monolithic） | 大部分功能（文件/设备/协议栈）都在内核态跑，跑得快但耦合大 | Linux、rCore |
| 微内核（microkernel） | 内核只留最基本的调度/IPC，其他服务跑在用户态，稳但要跨态 | Minix、L4、Fuchsia 早期 |

rCore 是教学宏内核。而 Hypervisor 是另一回事——它不是"内核内部怎么分层"，而是"**在硬件上再插一层，把一台机器切成多台**"。

## 虚拟化在做什么

一台物理机，同时跑多个"虚拟机"，每个里面以为自己独占一台机器：

```
┌──────────────┬──────────────┬──────────────┐
│  Guest OS A  │  Guest OS B  │  Guest OS C  │  各自以为自己是唯一 OS
├──────────────┴──────────────┴──────────────┤
│        Hypervisor / VMM                      │  管理所有虚拟机，分时复用硬件
├──────────────────────────────────────────────┤
│              物理硬件 (CPU/内存/网卡)          │
└──────────────────────────────────────────────┘
```

术语：

- **VMM（Virtual Machine Monitor）/ Hypervisor**：监控每个虚拟机、模拟硬件、分时复用物理资源的那层软件。
- **Guest（客户机）**：跑在上面的那个"假"操作系统，它通常不知道自己是 Guest。
- **Host（宿主机）**：真正管硬件的那层。

## 核心难题：Guest 想干危险的事怎么办

Guest OS 会去碰特权指令、读 CSR、改页表——这些在它自己的"世界"里是合法的，但在真实硬件上会挂掉或乱套。

解法是**陷入并模拟（trap & emulate）**：

```
Guest 执行一条特权指令(比如写 satp)
   │  CPU 发现它没权限 → 陷入到 Hypervisor
   ▼
Hypervisor: 这条指令到底啥意思？
   │  在 Guest 的虚拟硬件上"假装"执行一遍
   ▼
伪造一个结果返回给 Guest，Guest 毫无察觉
```

这和 Day69 的 trap 思路一模一样，只是这次陷入的目标是 Hypervisor，而不是普通内核。

## RISC-V H 扩展：硬件辅助虚拟化

早期虚拟化靠纯软件模拟，慢。硬件厂商加了**虚拟化扩展**，让 CPU 原生支持"跑 Guest"。RISC-V 的就是 **H 扩展（Hypervisor Extension）**。

它做的两件关键事：

### 1. 多了一层特权级和 VS 模式

在原来的 M/S/U 之上，H 扩展让 S 模式可以再分出一个 **VS 模式（Virtual Supervisor）**：Guest OS 跑在 VS 模式，它以为自己在管页表，其实是被 Hypervisor 管着的。

```
M  →  Hypervisor 跑在 M 或 Host S 模式
S/VS → Guest OS 跑在 VS 模式，以为自己是真 S
U  → Guest 里的用户程序
```

### 2. 两阶段地址翻译（two-stage address translation）

普通页表是一段翻译（Day70）。虚拟化要翻**两趟**：

```
Guest 虚拟地址 (GVA)
   │  第一阶段：Guest 自己的页表 (VS stage) → Guest 物理地址 (GPA)
   ▼
Guest 物理地址 (GPA)
   │  第二阶段：Hypervisor 控制的页表 (G stage) → 主机物理地址 (HPA)
   ▼
主机物理地址 (HPA)
```

第二阶段的页表根地址放在 **`hgatp`** 寄存器（hypervisor guest address translation and protection）。Hypervisor 通过它决定"这个 Guest 的 GPA 到底映射到主机哪块物理内存"——从而把每个 Guest 的内存隔离、并在主机上随意安置。

```
GVA ──[Guest 页表]──► GPA ──[hgatp 指向的页表]──► HPA
```

## 和前面知识的联系

你会发现后面这一切全是前面 19 天的叠加：

- 上下文切换（Day68）→ 切 Guest 也是保存恢复现场
- 中断/陷入（Day69）→ Guest 陷入 Hypervisor 模拟
- 页表（Day70）→ 变成两阶段翻译
- 进程隔离（Day72/73）→ 升级成虚拟机隔离

## 今日小结

- Hypervisor 把一台物理机切成多台虚拟机，Guest OS 以为自己独占硬件
- 核心机制是"陷入并模拟"：Guest 碰特权操作就陷到 VMM，由 VMM 假装执行
- RISC-V H 扩展：新增 VS 模式，地址翻译变两阶段（GVA→GPA→HPA），`hgatp` 管第二阶段
- 整套虚拟化是上下文切换、陷入、页表这些基础机制的再叠加

## 动手任务

1. 查资料区分 Type 1 Hypervisor（直接跑硬件上，如 Xen）和 Type 2（跑在 OS 上，如 VMware Workstation）。
2. 在 RISC-V 手册里找到 `hgatp`，对照 `satp` 理解它多管了一层什么。
3. 浏览开源操作系统训练营（learningos.cn）后续阶段内容，看看有没有 Hypervisor/arceos-hypervisor 相关的进阶实验。

**配合阅读**：[开源操作系统社区·learningos.cn（进阶 Hypervisor/虚拟化方向）](https://learningos.cn/)
