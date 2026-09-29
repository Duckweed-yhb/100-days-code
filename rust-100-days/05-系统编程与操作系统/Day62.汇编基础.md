# Day62 汇编基础

> 目标：看懂 RISC-V 最常用的寄存器和指令，搞懂函数调用时栈帧是怎么摆的——这样读 rCore 里那几屏 `__switch` 汇编不再像天书。

写内核躲不开汇编。好在 RISC-V 以**简洁（Reduced Instruction Set）**著称，常用指令就二三十条。今天不求会写复杂汇编，只求能读懂、能对着寄存器表查。

## 通用寄存器 x0~x31

RISC-V 有 32 个通用寄存器，叫 `x0`~`x31`，每个都有一个别名，按用途分工：

| 寄存器 | 别名 | 用途 | 谁来保护 |
|---|---|---|---|
| `x0` | `zero` | 永远读出来是 0，写进去无效 | — |
| `x1` | `ra` | 返回地址（return address），存函数返回处 | caller |
| `x2` | `sp` | 栈指针（stack pointer） | callee |
| `x3` | `gp` | 全局指针 | — |
| `x4` | `tp` | 线程指针 | — |
| `x5~x7` | `t0~t2` | 临时寄存器 | caller |
| `x8` | `s0/fp` | 保存寄存器 / 帧指针 | callee |
| `x9` | `s1` | 保存寄存器 | callee |
| `x10~x17` | `a0~a7` | 函数参数 / 返回值 | caller |
| `x18~x27` | `s2~s11` | 保存寄存器 | callee |
| `x28~x31` | `t3~t6` | 临时寄存器 | caller |

两个最重要的约定：

- **参数放 `a0~a7`**：前 8 个整数参数从 `a0` 开始排，返回值放 `a0`（放不下再用 `a1`）。系统调用编号也放 `a7`。
- **caller-saved / callee-saved**：调用别人前，调用方自己负责保存的寄存器叫 caller-saved（`t`、`a`、`ra`）；被调用函数如果要用 `s0~s11`、`sp`，必须自己存好、用完恢复，这叫 callee-saved。这条约定就是 Day68 上下文切换要存哪些寄存器的根据。

## 常用指令速查

RISC-V 指令都是定长 4 字节（压缩指令除外），格式规整：

```asm
# ==== 立即数加载 ====
li   a0, 10          # 把立即数 10 装进 a0 (伪指令，等价于 addi)
lui  a0, 0x12345     # 把立即数高 20 位加载到 a0 高位

# ==== 算术 ====
add  a0, a1, a2      # a0 = a1 + a2
addi a0, a0, 1       # a0 = a0 + 1 (加立即数)
sub  a0, a1, a2      # a0 = a1 - a2

# ==== 内存访问 (load/store，基址+偏移) ====
lw   a0, 0(sp)       # 从 sp+0 读 4 字节(字)到 a0
sd   a0, 8(sp)       # 把 a0 写 8 字节(双字)到 sp+8
# RISC-V 是 load-store 架构：不能直接内存加内存，先 load 到寄存器再算

# ==== 分支跳转 ====
beq  a0, a1, label   # if (a0==a1) 跳到 label
bne  a0, a1, label   # if (a0!=a1)
blt  a0, a1, label   # if (a0<a1)

# ==== 函数调用 ====
jal  ra, func        # 跳转到 func，把返回地址存进 ra
jalr ra, 0(a0)       # 跳到 a0 指向的地址，返回地址存 ra (间接跳转)
call func           # 伪指令 = jal ra, func
ret                 # 伪指令 = jalr zero, 0(ra)，跳回 ra 处
```

几个关键点：

- **`lw`/`sw`/`sd`**：`l` = load 读内存，`s` = store 写内存；`w` = word(4 字节)，`d` = doubleword(8 字节)。64 位 RISC-V 存寄存器用 `sd`（存 8 字节）。
- **跳转靠 `ra`**：`jal` 跳下一个函数时，自动把"回来的地址"写进 `ra`；函数末尾 `ret` 就是 `jalr zero, 0(ra)`，跳回去。
- **对比 x86**：x86 指令长短不一、隐式用栈传参，学习成本高；RISC-V 寄存器用途清晰、load-store 分离。rCore 选 RISC-V 很大一个原因就是它规整，适合教学。

## 栈帧与函数调用约定

调用一个函数时，栈是从高地址往低地址长的。以调用 `foo()` 为例：

```
高地址  ┌────────────────────┐
       │  调用者的栈帧        │
       ├────────────────────┤  ← sp 进入 foo 后先往下挪
       │  保存的 ra (返回地址)│
       │  保存的 s0 (旧帧指针)│
       │  foo 的局部变量      │
低地址  └────────────────────┘  ← sp
```

典型的函数序言/尾声：

```asm
foo:
    addi  sp, sp, -16    # 栈往下挪 16 字节，开栈帧
    sd    ra, 8(sp)      # 把 ra 存起来（因为 foo 可能还要调别人）
    sd    s0, 0(sp)      # 保存 callee-saved 的 s0
    # ... foo 干活，用 a0 当参数 ...
    ld    s0, 0(sp)      # 恢复 s0
    ld    ra, 8(sp)      # 恢复 ra
    addi  sp, sp, 16     # 栈帧回收
    ret                  # 跳回 ra
```

一句话记住：**调用方把参数塞 `a0~a7`，`call` 后 `ra` 指着下条指令；被调函数在自己栈帧里存好要保护的 `s` 寄存器，干完活 `ret` 回去。**

## 今日小结

- RISC-V 32 个寄存器：`zero`/`ra`/`sp` 是灵魂，参数用 `a0~a7`，临时用 `t`，保存用 `s`
- load-store 架构：内存操作只有 `lw/sd`，计算全在寄存器里做
- `jal/call` 跳转并记返回地址到 `ra`，`ret` 跳回
- 调用约定：caller 保护 `a/t/ra`，callee 保护 `s0~s11/sp`——这是上下文切换的依据

## 动手任务

1. 查 RISC-V 指令手册，确认 `beq`/`bne`/`blt`/`bge` 四个分支指令的区别。
2. 写一段伪汇编：在 `a0` 里算 `1+2+...+10`，循环用 `blt`。
3. 对着 rCore `__switch` 源码（`os/src/switch.S` 或 `context.rs`），数一下它存了哪几个寄存器，对照上表判断哪些是 callee-saved。

**配合阅读**：[rCore-Tutorial·第零章 操作系统概述（RISC-V 相关小节）](https://rcore-os.cn/rCore-Tutorial-Book-v3/chapter0/index.html)
