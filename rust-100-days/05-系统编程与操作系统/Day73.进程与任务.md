# Day73 进程与任务

> 目标：看懂 TaskControlBlock 里都存了一个进程的哪些档案，任务怎么在 Ready/Running/Blocked 之间流转，调度器怎么从协作式演进到抢占式。

在 rCore 里，"进程"被抽象成一个**任务（Task）**。它的全部档案放在 **TaskControlBlock（TCB，任务控制块）**里。

## TCB：一个进程的全部家当

```rust
// 内核代码片段，需在 rCore 环境运行，不能在普通 cargo 项目直接跑
pub struct TaskControlBlock {
    pub pid: Pid,                       // 进程号
    pub inner: SpinLock<TaskControlBlockInner>,
}

pub struct TaskControlBlockInner {
    pub task_status: TaskStatus,         // 状态：Ready/Running/Blocked
    pub task_cx: TaskContext,           // 上下文(Day68 的 __switch 用)
    pub memory_set: MemorySet,          // 地址空间(Day72)
    pub fd_table: Vec<Option<FileDesc>>, // 打开的文件表(ch6)
    pub exit_code: Option<i32>,         // 退出码
}
```

把这几样串起来：

| 字段 | 是什么 |
|---|---|
| `pid` | 进程身份证号 |
| `task_status` | 当前处于哪种状态 |
| `task_cx` | 被切走时寄存器存哪（栈上） |
| `memory_set` | 它的虚拟地址空间 |
| `fd_table` | 它打开了哪些文件 |

一个进程活着，本质就是这一坨数据在内核里。

## 任务状态机

任务在三态之间流转：

```
        ┌────────┐  被调度器选中   ┌──────────┐
        │ Ready  │ ────────────→ │ Running  │
        └────────┘               └──────────┘
            ↑   ↑                   │    │
   fork/唤醒 │   │ 时间片到/主动yield │    │ 等事件(读文件/等子进程)
            │   └───────────────────┘    ▼
            │                          ┌─────────┐
            └────── 事件到来被唤醒 ──────│ Blocked │
                                       └─────────┘
```

- **Ready**：就绪，能跑，就等 CPU
- **Running**：正在 CPU 上跑
- **Blocked**：阻塞，在等某件事（等键盘、等子进程退出），CPU 给它也没用

## TaskManager：所有任务的总账

内核有个全局的任务管理器，维护就绪队列：

```rust
// 内核代码片段，需在 rCore 环境运行，不能在普通 cargo 项目直接跑
pub struct TaskManager {
    ready_queue: VecDeque<Arc<TaskControlBlock>>,
}

impl TaskManager {
    fn add(&mut self, task: Arc<TaskControlBlock>) {
        self.ready_queue.push_back(task);
    }
    fn fetch(&mut self) -> Option<Arc<TaskControlBlock>> {
        self.ready_queue.pop_front()   // 先来先服务
    }
}
```

调度就是：`fetch` 出一个 Ready 任务，`__switch` 切过去。

## 协作式 vs 抢占式调度

这是 rCore 从 ch3 到 ch5 的关键升级：

| | 协作式(ch3) | 抢占式(ch5) |
|---|---|---|
| 什么时候切 | 任务**主动**调用 `yield` 才让 CPU | 时钟中断到了，OS**强制**切走 |
| 坏处 | 一个死循环任务能霸占 CPU | 要处理中断现场保存，复杂些 |
| 好处 | 简单，不用关中断 | 公平，不会被一个任务拖死 |

回忆 Day69：时钟中断处理里调 `scheduler::schedule()`——这就是抢占的硬件抓手。没有时钟中断，协作式下任务不主动让，谁也动不了它。

```
协作式:  任务A ——yield——> 任务B
抢占式: 任务A 跑着跑着, 时钟叮! ——OS 强制——> 任务B
```

## 当前任务：处理器抽象

CPU 同一时刻只跑一个，内核用一个 `Processor` 结构记"现在在跑谁"：

```rust
pub struct Processor {
    current: Option<Arc<TaskControlBlock>>,
    idle_task_cx: TaskContext,    // 没任务跑时切到的 idle 上下文
}
```

调度发生时：把当前任务放回 Ready 队列，`fetch` 下一个，`__switch` 过去。

## 今日小结

- TCB 是进程的全部档案：pid、状态、上下文、地址空间、文件表
- 三态机：Ready → Running → Blocked，事件驱动状态迁移
- TaskManager 管就绪队列，调度就是"取一个 Ready 任务切过去"
- 协作式靠任务主动 yield；抢占式靠时钟中断强制切，更公平

## 动手任务

1. 在 rCore `task/task.rs` 找到 `TaskStatus` 枚举，列出它的变体。
2. 追踪一次 `yield`：从用户态/内核态入口，看它怎么把当前任务放回队列再取下一个。
3. 把一个用户程序写成死循环，在协作式版本下观察它是否霸占 CPU；换到抢占式版本再看。

**配合阅读**：[rCore-Tutorial·第五章 进程与调度](https://rcore-os.cn/rCore-Tutorial-Book-v3/chapter5/index.html)
