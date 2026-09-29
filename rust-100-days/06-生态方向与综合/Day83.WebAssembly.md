# Day83 WebAssembly

> 目标：知道 Rust 怎么编译成 WASM 跑在浏览器里，能写出一个调用浏览器 alert 的最小例子，并看懂 wasm-bindgen / Trunk 各管什么。

## 什么是 WebAssembly

WebAssembly（WASM，Web 汇编）是一种二进制指令格式，最初设计来让 C/C++/Rust 这类语言**编译后跑在浏览器里**，性能接近原生。现在它也被用在边缘计算、命令行工具里。

Rust 官方就支持编译到 `wasm32-unknown-unknown` 这个目标——意思是「不依赖操作系统、跑在一个未知（通用）的 WASM 宿主上」。浏览器就是最常见的宿主。

## 整条工具链各管什么

写 Rust → WASM → 浏览器，你会遇到四个名字，先分清：

| 工具 | 干什么 | 大白话 |
|---|---|---|
| `wasm32-unknown-unknown` | 编译目标 | 告诉 cargo：编译成 WASM，不编出系统调用 |
| `wasm-bindgen` | JS↔Rust 互操作 | 在两边之间「翻译」函数和类型 |
| `web-sys` / `js-sys` | 浏览器 API 绑定 | 让你能调 `alert`、操作 DOM |
| `wasm-pack` / `Trunk` | 打包构建 | 把产物打成浏览器能直接用的包 |

- **js-sys**：绑定 JS 标准内置对象（`Number`、`Array`、`Promise`）
- **web-sys**：绑定浏览器 API（`window`、`document`、`alert`）
- **Trunk**：前端构建工具，自动跑 wasm-bindgen、生成 HTML、热更新，比手搓方便

## 装目标和工具

```powershell
# 加 WASM 编译目标
rustup target add wasm32-unknown-unknown
# 装 Trunk（前端构建）
cargo install trunk
```

## 最小例子：点按钮弹 alert

新建项目（Trunk 会帮你建结构）：

```powershell
cargo new --lib hello-wasm
cd hello-wasm
```

`Cargo.toml`：

```toml
[lib]
crate-type = ["cdylib"]   # 编译成动态库给 WASM 用

[dependencies]
wasm-bindgen = "0.2"
web-sys = { version = "0.3", features = ["Window", "Document", "HtmlElement"] }
js-sys = "0.3"
```

写代码 `src/lib.rs`：

```rust
use wasm_bindgen::prelude::*;
use web_sys::window;

// 这个宏把函数暴露给 JavaScript 调用
#[wasm_bindgen]
pub fn greet(name: &str) -> String {
    format!("你好，{}！这是 Rust 编译出来的", name)
}

// 页面加载时跑的入口：挂一个按钮点击事件
#[wasm_bindgen(start)]
pub fn start() {
    let window = window().expect("没有 window？");
    let document = window.document().expect("没有 document？");

    // 创建一个 <button>
    let button = document
        .create_element("button")
        .unwrap();
    button.set_text_content(Some("点我打个招呼"));

    // 把按钮加到页面上
    document.body().unwrap().append_child(&button).unwrap();
}
```

在项目根目录放一个 `index.html`，Trunk 会自动注入生成的 JS/WASM：

```html
<!DOCTYPE html>
<html>
  <head><meta charset="utf-8" /><title>Rust WASM</title></head>
  <body></body>
</html>
```

跑起来：

```powershell
trunk serve
```

浏览器打开它给的地址，点按钮，应该能看到 alert 弹出来。

## wasm-bindgen 在干嘛

`#[wasm_bindgen]` 是关键：它告诉工具链「这个 Rust 函数/类型要暴露给 JS」，并自动生成胶水代码（glue）。这样 JS 里就能：

```javascript
import init, { greet } from './hello_wasm.js';
await init();              // 加载 WASM 模块
console.log(greet("浮萍")); // 调用 Rust 函数
```

类型转换是它自动处理的：`&str` ↔ JS 字符串、`i32` ↔ JS number、结构体 ↔ JS 对象。你不用手写 FFI。

## 什么时候用 Rust+WASM

- **把 CPU 密集的库搬到前端**：比如图片处理、加密、解析器——JS 慢，Rust 快
- **和 Web 框架配合**：用 Leptos / Yew 这类 Rust 写前端框架（它们底层就是 WASM）
- **跨端共享代码**：同一份 Rust 逻辑跑在前端和后端

注意：**DOM 操作本身有开销**，别指望所有东西都用 Rust 写。重计算放 Rust，UI 框架帮你管 DOM。

## 今日小结

- Rust 可编译到 `wasm32-unknown-unknown`，在浏览器里跑
- `wasm-bindgen` 做 JS/Rust 互操作，`#[wasm_bindgen]` 暴露函数
- `web-sys` 绑定浏览器 API，`js-sys` 绑定 JS 标准库
- `Trunk` 一键构建/热更新，比手搓 wasm-pack 流程省事
- 适合搬重计算到前端，或用 Leptos/Yew 写 Rust 前端

## 动手任务

1. 装 `wasm32-unknown-unknown` 目标和 Trunk，跑通上面的 alert 例子
2. 改成点击按钮后往页面里插入一段文字（操作 `document.body`）
3. 把 `greet` 改成在 JS 里调用，观察浏览器控制台输出

**配合阅读**：[wasm-bindgen 仓库](https://github.com/rustwasm/wasm-bindgen)
