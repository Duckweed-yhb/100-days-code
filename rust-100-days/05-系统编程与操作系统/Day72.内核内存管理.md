# Day72 内核内存管理

> 目标：搞懂内核怎么管理物理页帧、怎么把一组逻辑段拼成一个进程的"内存集合 MemorySet"，以及内核自己的堆是怎么从 bump 演进到 buddy 的。

虚拟地址翻译（Day70）需要页表，页表要占物理页，进程代码/数据也要占物理页。谁来分配这些物理页？这就是**物理内存管理**。

## 两层视角

```
物理内存 (整个 DRAM，按 4KB 页帧切)
   │  ① 物理页帧分配器：哪一帧空闲、谁在用
   ▼
虚拟地址空间 (每个进程一套)
   │  ② 把"逻辑段"映射到物理帧，建页表
   ▼
进程看到的连续地址
```

- **物理页帧分配器（frame allocator）**：管理整个物理内存的空闲帧，`alloc()` 给一帧、`dealloc()` 还回去。
- **内存集合（MemorySet）**：一个进程的虚拟内存全景，里面放若干"逻辑段"，每段通过页表映射到物理帧。

## 物理页帧分配器

最朴素的实现：用一个**位图（bitmap）**或**空闲栈**记录哪些帧空闲。rCore 教学版用一个全局数组/集合管理空闲帧号：

```rust
// 内核代码片段，需在 rCore 环境运行，不能在普通 cargo 项目直接跑
pub struct FrameAllocator {
    next: usize,              // bump 指针：下一个空闲帧号
    total_frames: usize,
}

impl FrameAllocator {
    /// 取出一个空闲物理页帧
    fn alloc(&mut self) -> Option<PhysFrame> {
        if self.next >= self.total_frames { return None; }
        let frame = PhysFrame::from_ppn(self.next);
        self.next += 1;       // bump：往后挪一格，不管回收
        Some(frame)
    }
    fn dealloc(&mut self, _frame: PhysFrame) {
        // bump 分配器这里偷懒：不真回收
    }
}
```

这叫 **bump 分配器（ bump allocator）**：像蹭蹭往后顶，简单但不支持回收。够用就行——教学早期内存多的是。

真实内核会升级成：

| 算法 | 思路 | 优缺点 |
|---|---|---|
| bump | 指针往后顶 | 最简单，不支持回收 |
| 位图 | 每位代表一帧 | 省空间，分配慢要扫描 |
| 伙伴系统 buddy | 按 2 的幂合并/拆分 | 回收快、抗碎片，Linux 用它 |

## 逻辑段：进程内存长啥样

一个进程的虚拟地址空间由若干**逻辑段（MapArea）**组成：

```
高地址
┌──────────────┐
│  用户栈       │  (向下生长)
├──────────────┤
│  ...          │
├──────────────┤
│  数据段 .data │
├──────────────┤
│  代码段 .text │  (只读、可执行)
└──────────────┘ 低地址
```

每个段记录：起始虚拟地址、长度、权限（R/W/X/U）、内容从哪来。

```rust
// 内核代码片段，需在 rCore 环境运行，不能在普通 cargo 项目直接跑
pub struct MapArea {
    vpn_range: VPNRange,        // 这段占哪些虚拟页
    data_frames: BTreeMap...,    // 虚页 → 物理帧
    permission: PTEFlags,       // R/W/X/U
}

pub struct MemorySet {
    page_table: PageTable,       // 这套地址空间的根页表
    areas: Vec<MapArea>,        // 所有逻辑段
}
```

## 建一个进程地址空间

把用户程序 ELF 加载进内存的过程：

```
1. 分配一个空 MemorySet（建根页表）
2. 解析 ELF，为 .text/.rodata/.data 各建一个 MapArea
3. 给每段分配物理帧，把 ELF 内容拷进去
4. 把段映射写进页表（vpn → ppn，带权限）
5. 预留用户栈，映射一帧并写好初始 sp
6. 把根页表 PPN 写进 MemorySet，将来切进程时写 satp
```

## 内核自己的堆

内核也要 `Vec`/`Box`。Day63 说过要用 `#[global_allocator]` 挂一个分配器。它其实就是在物理页帧分配器之上，再包一层**字节级分配器**（比如 slab / bump），让你能 `alloc::vec![1,2,3]`。

```
alloc::vec![..] 申请 100 字节
   → 全局分配器(字节级) 在某个物理页里切
      → 不够了向物理页帧分配器再要一整页
```

## 今日小结

- 内存管理两层：物理页帧分配器管"哪帧空闲"，MemorySet 管"进程虚拟地址怎么映射"
- 页帧分配器从 bump 起步，进阶到位图/伙伴系统
- 一个进程 = 若干 MapArea（代码/数据/栈段）+ 一张页表
- 内核堆靠 `#[global_allocator]` 在页帧分配器之上再做字节级分配

## 动手任务

1. 在 rCore `mm/frame.rs` 找到分配器，判断它是 bump 还是位图。
2. 找到 `MemorySet::from_elf`，列出它建了哪几个 MapArea。
3. 算一算：一个 8MB 的物理内存，按 4KB 一页共有多少帧？位图要用多少字节？

**配合阅读**：[rCore-Tutorial·第四章 地址空间（内存管理）](https://rcore-os.cn/rCore-Tutorial-Book-v3/chapter4/index.html)
