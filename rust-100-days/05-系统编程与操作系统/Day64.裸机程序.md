# Day64 裸机程序

> 目标：把 Rust 编译成一个能在 QEMU 的 RISC-V 机器上裸奔的可执行文件，学会目标三元组、`#![no_main]` 和用 cargo-binutils 看二进制。

上一步我们扔掉了 `std`，但那还只是个"库"。要真正开机跑起来，得生成一个**没有操作系统外壳、直接放在内存里让 CPU 取指执行**的裸机（bare metal）可执行文件。

## 目标三元组（target triple）

Rust 编译器要知道：编译出来的代码跑在什么 CPU、什么操作系统、什么链接约定上。这个组合叫**目标三元组（target triple）**。

普通电脑是 `x86_64-unknown-linux-gnu` 或 `x86_64-pc-windows-msvc`——都带操作系统。我们的内核没有 OS，要用：

```
riscv64gc-unknown-none-elf
```

拆开看：

| 段 | 含义 |
|---|---|
| `riscv64` | 64 位 RISC-V |
| `gc` | 支持 G（原子指令扩展）和 C（压缩指令扩展） |
| `unknown` | 厂商无所谓 |
| `none` | **没有操作系统**（这是关键） |
| `elf` | 输出 ELF 格式可执行文件 |

先把这个目标装进工具链：

```powershell
rustup target add riscv64gc-unknown-none-elf
```

## #![no_main]：不要标准的 main 入口

普通 Rust 的 `main` 其实是被 C 运行时（`crt0`）调用的。裸机没有 C 运行时，也就没有"谁来调用 main"这回事。所以连 `main` 都不要了：

```rust
// 内核代码片段，需在 rCore 环境运行，不能在普通 cargo 项目直接跑
#![no_std]
#![no_main] // 不使用标准 main 入口约定

#[panic_handler]
fn panic(_info: &core::panic::PanicInfo) -> ! {
    loop {}
}
```

现在程序连入口都没有了——那 CPU 从哪开始执行？答案是：我们自己用汇编写一个 `_start` 当第一条指令（Day67 详讲），并通过链接脚本告诉链接器"入口是 `_start`"。

## 编译与查看产物

指定目标编译：

```powershell
cargo build --target riscv64gc-unknown-none-elf
```

产物在 `target/riscv64gc-unknown-none-elf/debug/` 下。它是个 ELF 文件，但还不是能直接塞进内存的纯二进制。我们需要 `cargo-binutils`（基于 LLVM 工具）来检查和转换：

```powershell
cargo install cargo-binutils
rustup component add llvm-tools-preview
```

常用命令：

```powershell
# 看符号表：确认 _start、rust_main 都在
cargo nm --target riscv64gc-unknown-none-elf

# 反汇编：把机器码翻回汇编，看入口长啥样
cargo objdump --target riscv64gc-unknown-none-elf -- -d

# 把 ELF 转成纯二进制(flat binary)，QEMU 才能按地址加载
cargo objcopy --target riscv64gc-unknown-none-elf -- \
    --output-target binary target/.../kernel target/.../kernel.bin

# 看程序大小：.text/.rodata/.data 各占多少
cargo size --target riscv64gc-unknown-none-elf -- -A
```

## 在 QEMU 里跑起来（概念）

rCore 用的是 `qemu-system-riscv64`，配合 OpenSBI 固件加载内核。命令大致是：

```powershell
qemu-system-riscv64 \
  -machine virt \
  -bios default `# 用内置 OpenSBI` \
  -kernel target/riscv64gc-unknown-none-elf/debug/os `# 内核 ELF` \
  -nographic `# 不开图形窗口，串口直接打到终端`
```

`-nographic` 让串口输出直接打印在你的终端，这就是 rCore 里 `println!` 最终显示的地方。退出 QEMU 用 `Ctrl+A` 然后按 `X`。

## 为什么要这么麻烦

对比一下：

```
普通 Rust 程序：
  源码 → rustc → ELF → OS(loader) 加载 → C 运行时调 main → 你的代码

裸机内核：
  源码(no_std/no_main) → rustc → ELF → objcopy 成 .bin →
  QEMU/固件 按固定地址加载 → 跳 _start → 你自己初始化一切
```

内核就是那个"加载完直接上电"的东西，所以它必须自己接管入口、栈、bss——没有任何现成的 runtime 帮你。

## 今日小结

- 裸机目标三元组：`riscv64gc-unknown-none-elf`，用 `rustup target add` 装上
- `#![no_std]` 去标准库，`#![no_main]` 去标准入口，`#[panic_handler]` 兜底 panic
- 产物是 ELF，用 `cargo-binutils`（nm/objdump/objcopy/size）检查和转纯二进制
- 内核靠 QEMU + OpenSBI 固件加载，`-nographic` 把串口输出打到终端

## 动手任务

1. `rustup target add riscv64gc-unknown-none-elf` 并确认 `rustup target list` 里它已安装。
2. 装 `cargo-binutils`，对一个 rCore 内核跑一次 `cargo nm`，找到 `_start` 和 `rust_main`。
3. 跑一次 `cargo size -- -A`，记录 `.text`、`.rodata`、`.data`、`.bss` 各自大小。

**配合阅读**：[rCore-Tutorial·第一章 应用程序与基本执行环境（目标平台与目标三元组）](https://rcore-os.cn/rCore-Tutorial-Book-v3/chapter1/index.html)
