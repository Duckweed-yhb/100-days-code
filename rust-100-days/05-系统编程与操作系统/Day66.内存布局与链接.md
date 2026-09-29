# Day66 内存布局与链接

> 目标：看懂链接脚本 `linker.ld`，知道内核的 `.text/.rodata/.data/.bss` 各放在物理内存哪里、栈顶是谁指定的，并用 cargo 把脚本传给链接器。

编译器只管把每个 `.rs` 文件编译成目标文件（`.o`），**谁把它们拼成一个内核、并决定每条代码/数据落在内存哪个地址？** 是链接器（linker）。告诉链接器怎么拼的，就是**链接脚本（linker script，`.ld`）**。

## 内核在物理内存里长什么样

QEMU 的 virt 机器会把内核加载到物理内存某个固定地址（rCore 里通常是 `0x80000000`，这是 RISC-V virt 机器 DRAM 的起始偏移）。从低到高典型布局：

```
物理地址低 ┌─────────────────────┐
          │  .text    代码段     │  机器指令，只读可执行
          │  .rodata  只读数据   │  字符串常量、只读表
          │  .data    已初始化数据│  有初值的全局变量
          │  .bss     零初始化区 │  没初值的全局变量，启动时清零
          ├─────────────────────┤
          │  内核栈 (向下长)     │  sp 从栈顶往下减
          │  ...                │
高地址     │  物理内存末尾        │
          └─────────────────────┘
```

几个段（section）的区别：

| 段 | 内容 | 要不要占文件体积 |
|---|---|---|
| `.text` | 编译出的指令 | 要 |
| `.rodata` | 只读常量 | 要 |
| `.data` | 有初值的全局变量 | 要（初值要存进文件） |
| `.bss` | 没初值的全局变量 | **不要**（只记大小，启动时清零） |

`.bss` 不占文件体积这个设计很省钱：一个 `static mut [u8; 1<<20]` 不用在磁盘上存 1MB 零，只要在脚本里说"预留 1MB"。

## 一个最小链接脚本

```ld
/* 内核代码片段：linker.ld，需在 rCore 环境使用，不能在普通 cargo 项目直接跑 */
BASE_ADDRESS = 0x80000000;   /* 内核加载的物理起始地址 */

ENTRY(_start)                 /* 指定程序入口符号是 _start */

SECTIONS {
    . = BASE_ADDRESS;         /* 当前位置计数器从这里开始 */

    .text : {                 /* 代码段 */
        *(.text.entry)        /* 入口块放最前面，_start 必须第一个 */
        *(.text .text.*)      /* 其余代码 */
    }

    .rodata : { *(.rodata .rodata.*) }   /* 只读数据 */

    .data : { *(.data .data.*) }         /* 已初始化数据 */

    .bss : {
        __bss_start = .;      /* 记下 bss 起始地址，启动代码要清零用 */
        *(.sbss .bss .bss.*)
        __bss_end = .;        /* bss 结束地址 */
    }

    /* 把栈顶符号暴露给 Rust：物理内存最高处当栈顶 */
    PROVIDE(boot_stack_top = 0x88000000);
}
```

逐行解释：

- `ENTRY(_start)`：告诉 QEMU/固件"跳到这个符号开始跑"。
- `. = BASE_ADDRESS`：位置计数器从 `0x80000000` 排起。
- `*(.text.entry)`：带 `#[link_section = ".text.entry"]` 的代码（就是 `_start`）必须排第一，保证它在最开头。
- `__bss_start`/`__bss_end`：我们在脚本里造两个符号，Day67 的启动代码靠它们知道要把哪一段清零。
- `PROVIDE(boot_stack_top = ...)`：把"栈顶地址"作为符号暴露出去，启动时 `sp` 就设到这里。

## 怎么让 cargo 用这个脚本

cargo 默认调系统链接器，我们要额外传 `-T linker.ld`。做法是建一个 `.cargo/config.toml`：

```toml
# .cargo/config.toml（这是项目配置文件，不是内核代码）
[target.riscv64gc-unknown-none-elf]
rustflags = [
    "-Clink-arg=-Tsrc/linker.ld",   # 把链接脚本传给 ld
    "-Cdefault-linker-libraries=path/to/ld",  # 用 riscv 交叉链接器
]
```

之后 `cargo build --target riscv64gc-unknown-none-elf` 就会自动用你的脚本。改了 `.ld` 后用 `cargo objdump` 反汇编，能看到地址布局确实按脚本排了。

## 对齐与位置计数器

脚本里那个 `.`（dot）叫**位置计数器**，代表"当前排到哪个地址"。常见技巧：

```ld
. = ALIGN(4K);   /* 把后面的段对齐到 4KB，方便做页表 */
```

内存管理（Day70/72）大量用 4KB 对齐，就是因为页表以页为单位。链接脚本里提前对齐，后面才不会手忙脚乱。

## 今日小结

- 链接脚本决定内核各段落在物理内存哪里；`ENTRY(_start)` 指定入口
- 四段：`.text` 代码、`.rodata` 只读、`.data` 有初值、`.bss` 零初始化（不占文件）
- 脚本里造 `__bss_start/__bss_end` 和 `boot_stack_top` 符号，供启动代码用
- 用 `.cargo/config.toml` 里的 `rustflags` 把 `-T linker.ld` 传给链接器

## 动手任务

1. 打开 rCore 的 `src/linker.ld`，找到 `BASE_ADDRESS`、`__bss_start`、`boot_stack_top` 三个符号。
2. 跑 `cargo size -- -A`，对照脚本确认 `.bss` 大小和你代码里的零初始化全局变量对得上。
3. 故意把 `ENTRY(_start)` 改错，观察链接/启动时发生什么。

**配合阅读**：[rCore-Tutorial·第一章 应用程序与基本执行环境（调整内核的内存布局）](https://rcore-os.cn/rCore-Tutorial-Book-v3/chapter1/index.html)
