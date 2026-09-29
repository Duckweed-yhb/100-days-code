# Day79 unikernel

> 目标：搞懂传统操作系统为什么"臃肿"，unikernel 怎么把应用和内核编译成一个最小单用途镜像，以及 rcore-os 社区的 ArceOS 是怎么落地这个思路的。

前面我们写的 rCore 是个"通用内核"：一个内核跑一堆互不认识的应用。今天看另一种思路——**unikernel（单内核/库操作系统）**。

## 传统 OS 的浪费在哪

想想你在云服务器上跑一个极简 Web 服务：

```
一台 VM 里:
  Linux 内核(几十 MB，带网卡/文件/内存/调度几百个驱动和子系统)
  + systemd / sshd / cron ... 一堆你根本不用的系统服务
  + 你的应用程序
```

问题：

- 内核带着你用不到的几百个功能，攻击面巨大。
- 启动慢、镜像大、内存占用高。
- 应用和内核之间还要反复系统调用、跨用户/内核态边界，有开销。

## unikernel：把应用和内核缝在一起

unikernel 的思路反过来：**你的应用直接链接进一组定制的内核组件，编译成一个只能干一件事的、最小的镜像。**

```
传统:   [通用内核] ←系统调用→ [应用A] [应用B] [应用C]

unikernel:
        只给 [我的 Web 应用] 配上它需要的那几个内核组件
        → 编译成一个单用途镜像，启动即跑，没有别的应用
```

关键词：

- **库操作系统（library OS）**：内核功能不再是一个独立运行的大程序，而是一组**库**，应用像依赖普通 crate 一样链接它们。
- **单用途（single-purpose）**：一个镜像只为一个应用定制，不要的组件直接不链接进来。
- **裁剪（specialize）**：编译器看应用真正用到哪些内核功能，把用不到的全部裁掉。

## 好处与代价

| 维度 | 好处 | 代价 |
|---|---|---|
| 体积 | 镜像从几百 MB 缩到几 MB | 失去通用性 |
| 启动 | 毫秒级启动，适合 Serverless/边缘 | 一个应用一个镜像 |
| 安全 | 攻击面极小，没多余服务 | 调试/运维方式不一样 |
| 性能 | 少了用户/内核态切换 | 应用 bug 可能直接带崩整个镜像 |

特别适合：云原生微服务、Serverless、边缘计算、固件。

## ArceOS：rcore-os 的 unikernel 实验

**ArceOS** 是 rcore-os 社区用 Rust 写的一个**模块化（modular）操作系统 / unikernel**，灵感来自 Unikraft。它把内核拆成一个个可组合的 crate 模块：

```
arceos/
├── modules/
│   ├── axhal/      硬件抽象(平台相关)
│   ├── axalloc/    分配器
│   ├── axmm/       内存管理
│   ├── axtask/     任务/调度
│   ├── axfs/       文件系统
│   ├── axnet/       网络(TCP/UDP, 基于 smoltcp)
│   └── axsync/     同步原语
└── ulib/axstd/     给应用用的、模仿 std 的上层库
```

它的特点（来自项目 README）：

- 多架构：x86_64、RISC-V64、AArch64、LoongArch64
- 多线程，FIFO/RR/CFS 调度
- VirtIO 网卡/块设备/GPU 驱动，TCP/UDP 协议栈
- SMP 多核调度（per-CPU 运行队列）
- 兼容 Linux 应用

写应用时，你新建一个 `no_std no_main` 的 Rust 包，依赖 `axstd`，按需要开启 feature（`alloc`/`paging`/`net`...），再用 `make` 把你的应用和选定模块一起编译成镜像——这就是 unikernel 的标准玩法。

## 和 rCore 的关系

```
rCore-Tutorial: 教学用，一步步从零写通用内核，帮你理解原理
ArceOS:         工业探索用，把内核做成可组合的模块化 unikernel
```

学完 rCore 再看 ArceOS，你会发现它每个模块（axtask、axmm、axfs...）名字眼熟——就是把 rCore 里讲过的子系统，重构成了可独立复用的 crate。

## 今日小结

- 传统通用内核为一个应用带了一堆用不到的功能，臃肿、攻击面大
- unikernel/库操作系统：应用 + 定制内核组件编译成单用途最小镜像
- 好处：小、启动快、攻击面小；代价：失去通用性，一应用一镜像
- ArceOS 是 rcore-os 用 Rust 写的模块化 unikernel，内核拆成 axhal/axmm/axtask/axfs/axnet 等可组合 crate

## 动手任务

1. 浏览 ArceOS 仓库 `modules/` 目录，数一下它拆出了多少个模块。
2. 跑通官方 `app-helloworld`，看它怎么用 `axstd` 代替 `std`。
3. 对比：rCore 的 task 模块和 ArceOS 的 axtask，接口设计上有什么异同。

**配合阅读**：[ArceOS·GitHub（Rust 模块化 unikernel）](https://github.com/rcore-os/arceos)
