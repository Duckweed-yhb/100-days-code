# Day84 嵌入式

> 目标：搞清楚 Rust 写单片机的分层模型（PAC / HAL / board crate），认识 no_std、运行时、烧录调试这一整套工具链。

## 嵌入式 Rust 是什么

嵌入式就是写单片机（MCU，微控制器）——比如 STM32、ESP32、树莓派 PICO 这种几十 KB 内存的芯片。这里跑不了操作系统，标准库 `std` 也用不上，所以你要回到 Day63 学的 **no_std**：只用 `core` 核心库。

好处是 Rust 的所有权和类型安全在资源紧张的芯片上特别香——没有 GC 占用，没有 C 那种裸指针满天飞的 bug。

## 三层结构：PAC → HAL → board

操作一个芯片，社区约定俗成分三层，从下到上：

| 层 | 全称 | 干什么 | 怎么来的 |
|---|---|---|---|
| PAC | Peripheral Access Crate（外设访问层） | 直接读写寄存器，最底层 | 用 `svd2rust` 从芯片厂提供的 SVD 文件自动生成 |
| HAL | Hardware Abstraction Layer（硬件抽象层） | 把寄存器操作包装成 `OutputPin`、`Delay` 这类好用的 trait | 社区手写，实现 `embedded-hal` 标准 trait |
| board crate | 板级支持包 | 针对具体开发板，把引脚名（如 `PD13`）包装成「板上用户 LED」 | 板厂/社区提供 |

大白话：PAC 是「对着寄存器地址硬刚」，HAL 是「把灯抽象成一个能 `set_high()` 的对象」，board crate 是「这块板上第几个灯」。

### embedded-hal：跨芯片的通用 trait

`embedded-hal` 定义了一套标准接口，比如 `OutputPin`（能点亮）、`InputPin`（能读按键）、`DelayUs`（能延时）。只要某芯片的 HAL 实现了这些 trait，你的点灯代码换个芯片照样编译。这就是 Rust trait 在嵌入式里的威力。

## 运行时：cortex-m 与 riscv-rt

单片机没有操作系统帮你启动。你需要一个「运行时 crate」来负责：初始化栈、把数据从 Flash 搬到 RAM、调 `main`。

- Cortex-M 芯片（STM32 等）：用 `cortex-m` + `cortex-m-rt`，入口用 `#[entry]`
- RISC-V 芯片（ESP32-C3 等）：用 `riscv-rt`

```rust
#![no_std]
#![no_main]

use cortex_m_rt::entry;
use panic_halt as _;  // panic 时停住（最小占位）

#[entry]              // 程序真正的入口，替代 main
fn main() -> ! {     // 嵌入式程序不返回
    loop {
        // 点灯逻辑
    }
}
```

注意 `-> !`（发散类型）：单片机程序永远不退出。

## 日志：defmt

单片机没有 `println!` 那种奢侈——打印一个字符串要占用宝贵的 Flash 和串口带宽。**defmt**（deferred formatting，延迟格式化）的做法是：**格式化信息放在主机端，单片机只发极短的标签和参数**，省带宽省空间。

```rust
use defmt::info;

info!("系统启动，计数 = {}", 42);  // 像 println! 一样写
```

配合 `probe-rs run` 就能在电脑上看到漂亮的结构化日志。

## 烧录调试：probe-rs

以前烧单片机要用厂商专用工具链。**probe-rs** 是 Rust 社区做的统一工具，一根调试器（如 DAPLink、J-Link）就能烧录和调试：

```powershell
cargo install probe-rs-tools

# 一条命令：编译 + 烧录 + 看 RTT/defmt 日志
probe-rs run
```

它支持 Cortex-M 和 RISC-V，包括 ESP32-C3。

## 异步嵌入式：Embassy

传统嵌入式是裸循环/中断。**Embassy** 是一个嵌入式异步框架，让你在单片机上也能写 `async/await`，管理定时器、UART、USB 等外设，社区非常活跃。

## 今日小结

- 嵌入式 Rust = no_std，跑在单片机上，没有操作系统
- 三层模型：PAC（寄存器，svd2rust 生成）→ HAL（实现 embedded-hal trait）→ board crate
- 运行时由 `cortex-m-rt` / `riscv-rt` 提供，入口用 `#[entry]`，`main` 返回 `!`
- 日志用 defmt（延迟格式化，省带宽），烧录调试用 probe-rs
- 想要异步外设用 Embassy

## 动手任务

1. 翻一遍 The Embedded Rust Book 的目录，对每个名词有个印象即可
2. 如果你手头有 STM32 / Pico / ESP32-C3，照官方板子 README 点亮一颗 LED
3. 没有硬件也没关系：搞懂 PAC/HAL/board 三层各自解决什么问题，画一张图

**配合阅读**：[The Embedded Rust Book](https://docs.rust-embedded.org/book/)
