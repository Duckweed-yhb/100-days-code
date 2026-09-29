# Day88 Tauri 桌面应用

> 目标：搞懂 Tauri 2 的架构（Rust 核心 + 系统 WebView），知道它为什么比 Electron 小那么多，并能描述一个最小 Tauri 应用的结构。

## 用 Web 技术写桌面应用

很多人想用 HTML/CSS/JS 写桌面应用（因为前端生态熟）。以前的标准答案是 **Electron**（VS Code、Discord 都是它）。但 Electron 每个应用都要**打包一个完整的 Chromium 浏览器**，安装包动辄 100~200 MB，内存占用几百 MB。

**Tauri** 换了个思路：**不打包浏览器，而是用操作系统自带的 WebView**。

## Tauri 2 的架构

| | Electron | Tauri 2 |
|---|---|---|
| 渲染引擎 | 自带 Chromium（每个 app 一份） | 系统 WebView（Win 用 WebView2，mac 用 WKWebView，Linux 用 WebKitGTK） |
| 后端 | Node.js | **Rust** |
| 安装包体积 | 100~200 MB | 通常 3~15 MB |
| 内存占用 | 150~400 MB | 50~100 MB |
| 安全模型 | Node 有完整系统权限 | 前端默认不可信，能力白名单 |

大白话：Electron 是「随身带一个浏览器」，Tauri 是「借用系统已有的浏览器，自己只写个 Rust 核心」。所以包小、内存低、启动快。

## 最小应用结构

Tauri 项目分两块：**前端**（任意框架，甚至纯 HTML）+ **Rust 后端**。

```
my-app/
├── src/                  # 前端：HTML/CSS/JS（或 React/Vue）
│   └── main.html
├── src-tauri/            # Rust 后端
│   ├── src/
│   │   └── main.rs       # Rust 入口
│   ├── tauri.conf.json   # Tauri 配置：窗口、权限
│   └── Cargo.toml
└── package.json
```

### Rust 后端：暴露命令给前端

前端不能直接操作系统，要通过「命令」调 Rust：

```rust
// src-tauri/src/main.rs
#[tauri::command]              // 把这个函数暴露给前端
fn greet(name: &str) -> String {
    format!("你好，{}！来自 Rust 后端", name)
}

#[cfg_attr(mobile, tauri::mobile_entry_point)]
pub fn run() {
    tauri::Builder::default()
        .invoke_handler(tauri::generate_handler![greet])  // 注册命令
        .run(tauri::generate_context!())
        .expect("启动失败");
}
```

### 前端：调用 Rust 命令

```javascript
// 前端 JS 里，invoke 调用 Rust 函数
import { invoke } from '@tauri-apps/api/core';

const msg = await invoke('greet', { name: '浮萍' });
console.log(msg);  // 你好，浮萍！来自 Rust 后端
```

这就是 Tauri 的核心交互模型：**前端负责界面，Rust 负责干重活（文件、系统调用、性能计算），两边通过 `invoke` 通信**。

## 脚手架起步

```powershell
# 装脚手架（需要先有 Node.js）
npm create tauri-app@latest
# 按提示选前端框架，然后：
cd my-app
npm install
npm run tauri dev    # 开发模式，热更新
```

## 什么时候选 Tauri

- 想要小体积、低内存的桌面工具
- 团队会前端，又想要 Rust 的性能和安全
- 注意：**三个系统的 WebView 不是同一个引擎**，渲染一致性不如 Electron（比如老 Windows 上 WebView2 行为可能有差异）

## 今日小结

- Tauri = Rust 后端 + 系统 WebView，不打包 Chromium
- 相比 Electron：包小一个数量级（几 MB vs 上百 MB）、内存低
- 前端用任意 Web 框架，通过 `invoke` 调 Rust 命令
- Rust 命令用 `#[tauri::command]` 暴露
- 代价是三平台 WebView 渲染不完全一致

## 动手任务

1. 装 Tauri 脚手架，跑通官方 hello world
2. 加一个 Rust 命令：读取某个文件内容返回给前端显示
3. 对比一下 Tauri 打包后的安装包体积，和你印象里的 Electron 应用比

**配合阅读**：[Tauri 官网](https://tauri.app/)
