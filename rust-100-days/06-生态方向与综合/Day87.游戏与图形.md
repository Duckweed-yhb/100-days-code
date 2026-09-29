# Day87 游戏与图形

> 目标：认识 Rust 游戏/图形生态的三大件——Bevy 游戏引擎、wgpu 图形 API、macroquad 轻量入门，理解 ECS 是什么。

## Rust 做游戏图形的现状

Rust 在游戏行业还不算主流（大厂仍用 C++/Unity/Unreal），但独立游戏和工具链里越来越火。它的优势是：性能接近 C++，但内存安全、迭代快。生态分三层：

| 层 | 代表 | 干什么 |
|---|---|---|
| 完整游戏引擎 | **Bevy** | 2D/3D 游戏一条龙，ECS 架构 |
| 底层图形 API | **wgpu** | 跨平台实现 WebGPU 标准，自己写渲染 |
| 轻量入门 | **macroquad** | 几百行做个小游戏，上手快 |

## Bevy：数据驱动的 ECS 引擎

Bevy 是 Rust 最火的开源游戏引擎，核心是 **ECS（Entity Component System，实体-组件-系统）**。

传统游戏是「一个对象里塞数据和方法」（面向对象）。ECS 反过来：

- **Entity（实体）**：一个「东西」，只是个 ID
- **Component（组件）**：纯数据结构体，挂在实体上（位置、速度、血量）
- **System（系统）**：普通函数，遍历带某些组件的实体，更新它们

好处是数据连续排布在内存里，**缓存友好、天然并行**——这也是为什么 Bevy 跑得飞快。

### 三个概念长什么样

```rust
use bevy::prelude::*;

// 1) Component：纯数据，挂在实体上
#[derive(Component)]
struct Position { x: f32, y: f32 }

#[derive(Component)]
struct Velocity { dx: f32, dy: f32 }

// 2) Resource：全局唯一的单例数据（比如游戏时间）
#[derive(Resource, Default)]
struct GameTime(f32);

// 3) System：普通函数，查询带 Position+Velocity 的实体并更新
fn move_system(mut query: Query<(&mut Position, &Velocity)>) {
    for (mut pos, vel) in query.iter_mut() {
        pos.x += vel.dx;
        pos.y += vel.dy;
    }
}

fn main() {
    App::new()
        .add_plugins(DefaultPlugins)
        .init_resource::<GameTime>()
        .add_systems(Update, move_system)  // 把系统挂进去
        .run();
}
```

记住这个套路：**组件是数据，系统是函数，资源是全局单例**。Bevy 会自动并行调度所有系统。

## wgpu：跨平台图形 API

如果你不想用整个引擎，只想自己控制渲染，用 **wgpu**。它实现了 **WebGPU 标准**，一套代码跑在 Windows / macOS / Linux / 浏览器底层。它是很多上层库（包括 Bevy 的渲染）的底座。

概念上你要和「管线（pipeline）」「着色器（shader）」「缓冲区」打交道，门槛较高，先知道它是图形底层即可。

## macroquad：最快上手

想周末做个打砖块小游戏？用 **macroquad**。它 API 极简，几百行就能出东西：

```rust
// 概念示意：macroquad 风格，感受极简
// use macroquad::prelude::*;
//
// #[macroquad::main("我的游戏")]
// async fn main() {
//     loop {
//         clear_background(LIGHTGRAY);
//         draw_text("Hello Rust Game!", 20.0, 40.0, 30.0, DARKGRAY);
//         next_frame().await;
//     }
// }
```

## 选型建议

- 想正经做游戏、学 ECS：**Bevy**
- 只想快速出个小游戏原型：**macroquad**
- 想自己写渲染管线、做可视化工具：**wgpu**

## 今日小结

- Bevy 是 Rust 主流游戏引擎，核心是 ECS
- ECS：实体是 ID，组件是纯数据结构体，系统是遍历组件的函数
- Resource 是全局单例数据；Bevy 自动并行调度系统
- wgpu 是跨平台 WebGPU 图形 API，是渲染底层
- macroquad 轻量入门，快速做小游戏原型

## 动手任务

1. 装 Bevy，跑通官方「2D 精灵移动」示例
2. 给上面的 `move_system` 加一个边界判断：物体飞出屏幕就弹回来
3. （可选）用 macroquad 写一个屏幕上跟着鼠标移动的方块

**配合阅读**：[Bevy 官网](https://bevyengine.org/)
