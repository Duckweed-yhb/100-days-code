# Day74 系统调用

> 目标：把"用户程序请求内核服务"这条链路彻底讲透——ecall 怎么触发、参数怎么传、编号怎么分发，以及 write/exit/sleep/fork/exec/waitpid 各自干了啥。

用户程序是哑巴：它不能直接读键盘、不能直接关机、不能直接创建进程。它能做的只有一件事——**请求内核帮忙**。这个请求通道就是**系统调用（system call）**。

## 为什么需要系统调用

特权级隔离的必然结果：

- 用户态（U）碰不了硬件、改不了页表、杀不了进程。
- 这些危险操作只能在内核态（S）做。
- 于是硬件提供了一扇"安全的门"：`ecall`。用户态执行 `ecall`，CPU 陷入内核，由内核检查参数后代为执行。

```
用户程序: "我要 write 字符"
   ecall  ──────────────►  内核: 检查参数, 真的去写
   ◄──────────────  返回结果
```

## 参数怎么传：寄存器约定

RISC-V 系统调用走寄存器，和 Day62 的函数调用约定一脉相承：

| 寄存器 | 作用 |
|---|---|
| `a7` (x17) | **系统调用编号**（内核据此知道你要哪个服务） |
| `a0~a5` (x10~x15) | 最多 6 个参数 |
| `a0` (x10) | **返回值** |

回忆 Day69 的 trap_handler：

```rust
// 内核代码片段，需在 rCore 环境运行，不能在普通 cargo 项目直接跑
Trap::Exception(Exception::UserEnvCall) => {
    cx.sepc += 4;   // 跳过 ecall 这条指令本身
    cx.x[10] = syscall(
        cx.x[17],                       // a7 = 编号
        [cx.x[10], cx.x[11], cx.x[12], // a0..a2 = 参数
         cx.x[13], cx.x[14], cx.x[15],
        ]
    ) as usize;                         // 返回值写回 a0
}
```

`sepc += 4` 很关键：ecall 本身是 4 字节指令，陷进来时 `sepc` 还指着它，处理完若直接 `sret` 会再次执行 ecall 死循环，所以要往后挪 4 字节。

## 编号分发：syscall()

```rust
// 内核代码片段，需在 rCore 环境运行，不能在普通 cargo 项目直接跑
pub fn syscall(id: usize, args: [usize; 6]) -> isize {
    match id {
        SYSCALL_WRITE  => sys_write(args[0], args[1] as *const u8, args[2]),
        SYSCALL_EXIT   => sys_exit(args[0] as i32),
        SYSCALL_SLEEP  => sys_sleep(args[0]),
        SYSCALL_FORK   => sys_fork(),
        SYSCALL_EXEC   => sys_exec(args[0] as *const u8),
        SYSCALL_WAITPID=> sys_waitpid(args[0] as Pid, ...),
        _ => panic!("unknown syscall id {}", id),
    }
}
```

## 几个核心系统调用在干嘛

| 编号(概念) | 作用 | 内核动作 |
|---|---|---|
| `write` | 输出字符 | 把缓冲区内容写到串口/文件 |
| `exit` | 进程退出 | 标记 TCB 为 Zombie，记退出码 |
| `sleep` | 睡一会 | 把任务设 Blocked，设个闹钟，到点唤醒 |
| `fork` | 复制当前进程 | 克隆 MemorySet/TCB，父子都返回 |
| `exec` | 换成新程序 | 清空地址空间，加载新 ELF 重跑 |
| `waitpid` | 等子进程结束 | 子进程没结束就 Blocked，结束后收尸 |

`fork` 是 Unix 的灵魂：它**完整复制**当前地址空间造出一个子进程。父子从 `fork` 返回处继续跑，区别只在返回值——父进程拿到子 pid，子进程拿到 0。

```
fork() 返回后:
  父进程:  a0 = 子进程 pid
  子进程:  a0 = 0   ← 靠这个区分自己是谁
```

## 完整一次 write(1, "hi", 2)

```
1. 用户 libc: a7=64(write), a0=1(fd), a1="hi"指针, a2=2; ecall
2. 硬件陷进内核, __alltraps 存寄存器
3. trap_handler 识别 UserEnvCall
4. sepc += 4
5. syscall(64, [1, ptr, 2]) → sys_write
6. sys_write 把 'h','i' 写进串口
7. 返回写入字节数 2, 写回 a0
8. __alltraps_return 恢复寄存器, sret 回用户
```

## 今日小结

- 系统调用是用户态请求内核服务的唯一安全通道，靠 `ecall` 陷入
- 约定：`a7` 是编号，`a0~a5` 是参数，`a0` 是返回值
- `sepc += 4` 跳过 ecall，否则死循环
- 核心调用：write/exit/sleep/fork/exec/waitpid；fork 靠返回值区分父子

## 动手任务

1. 在 rCore `syscall/` 目录列出所有 syscall 编号常量。
2. 读 `sys_fork`，看它怎么克隆 MemorySet。
3. 写一个用户程序，连续调 `sys_sleep(1)` 打印，观察任务在 Blocked/Ready 间切换。

**配合阅读**：[rCore-Tutorial·第五章 进程与调度（系统调用）](https://rcore-os.cn/rCore-Tutorial-Book-v3/chapter5/index.html)
