# Day54 多线程 Web 服务器 1

> 目标：基于 Day52 的 HTTP 服务器，写一个能返回 HTML 文件的单线程 Web 服务器，并亲手体验单线程“慢请求堵死一切”的问题，为明天的多线程改造埋下伏笔。

## 今天的目标

我们要做一个真实一点的 Web 服务器：

- 浏览器访问 `/` → 返回 `hello.html`（200）
- 访问其他路径 → 返回 `404.html`（404）
- 访问 `/sleep` → 故意等 5 秒再返回，用来暴露单线程的问题

今天先做**单线程**版本，明天再改成线程池版。

## 准备 HTML 文件

在项目根目录建两个文件：

`hello.html`：

```html
<!DOCTYPE html>
<html>
  <head><meta charset="utf-8"><title>首页</title></head>
  <body><h1>你好</h1><p>这是 Rust 单线程服务器返回的页面。</p></body>
</html>
```

`404.html`：

```html
<!DOCTYPE html>
<html>
  <head><meta charset="utf-8"><title>404</title></head>
  <body><h1>404</h1><p>页面不存在。</p></body>
</html>
```

## 完整的单线程服务器

```rust
use std::io::prelude::*;
use std::net::TcpListener;
use std::net::TcpStream;
use std::fs;
use std::thread;
use std::time::Duration;

fn main() {
    let listener = TcpListener::bind("127.0.0.1:7878").unwrap();
    println!("服务器启动: http://127.0.0.1:7878");

    // 单线程：一个一个地处理连接
    for stream in listener.incoming() {
        let stream = stream.unwrap();
        handle_connection(stream);
    }
}

fn handle_connection(mut stream: TcpStream) {
    // 1. 读请求
    let mut buffer = [0; 1024];
    let n = stream.read(&mut buffer).unwrap();
    let request = String::from_utf8_lossy(&buffer[..n]);
    println!("收到请求:\n{}", request);

    // 2. 解析请求行，判断路径
    let get = b"GET / HTTP/1.1\r\n";
    let sleep = b"GET /sleep HTTP/1.1\r\n";

    let (status_line, filename) = if request.starts_with(get) {
        ("HTTP/1.1 200 OK\r\n", "hello.html")
    } else if request.starts_with(sleep) {
        // 故意睡 5 秒，模拟一个慢请求
        thread::sleep(Duration::from_secs(5));
        ("HTTP/1.1 200 OK\r\n", "hello.html")
    } else {
        ("HTTP/1.1 404 NOT FOUND\r\n", "404.html")
    };

    // 3. 读 HTML 文件，拼响应
    let contents = fs::read_to_string(filename).unwrap();
    let response = format!(
        "{}Content-Type: text/html; charset=utf-8\r\nContent-Length: {}\r\n\r\n{}",
        status_line,
        contents.len(),
        contents
    );

    stream.write_all(response.as_bytes()).unwrap();
    stream.flush().unwrap();
}
```

运行：

```powershell
cargo run
```

## 验证

浏览器访问：

- http://127.0.0.1:7878/ → 立即显示首页
- http://127.0.0.1:7878/abc → 显示 404
- http://127.0.0.1:7878/sleep → **等 5 秒**才显示首页

## 亲手暴露单线程的问题

这是今天最关键的一步。保持服务器运行：

1. 浏览器先打开一个标签页访问 http://127.0.0.1:7878/sleep
2. 在它等 5 秒的过程中，**立刻**在另一个标签页访问 http://127.0.0.1:7878/

你会发现：第二个请求也卡住了，直到第一个 `/sleep` 请求 5 秒结束后才一起返回。

**原因**：`for stream in listener.incoming()` 是同步的——处理完一个连接（`handle_connection` 返回）才会去接下一个。`/sleep` 在 `handle_connection` 里 `sleep(5s)`，这 5 秒整个服务器都在等，什么新连接都接不了。

这就是单线程服务器的致命问题：**一个慢请求拖垮所有用户**。

## 一个 naive 的改进：每个连接开一个线程

直觉上很简单——把 `handle_connection` 放到新线程里：

```rust
for stream in listener.incoming() {
    let stream = stream.unwrap();
    thread::spawn(move || {
        handle_connection(stream);   // 每个连接一个新线程
    });
}
```

这样 `/sleep` 在新线程里睡，主线程立刻能接下一个连接。试试，问题好像解决了。

但这有个隐患：**不能无限开线程**。如果突然来 1 万个请求，系统就会开 1 万个线程，内存和 CPU 都扛不住，甚至被 DoS（拒绝服务）打死。

真实做法是：预先开固定数量的线程（比如 4 个），请求来了排进队列，空闲线程取任务执行。这就是**线程池（thread pool）**，明天我们从零实现它。

## 今日小结

- 单线程服务器：`TcpListener::incoming()` 循环里同步调 `handle_connection`
- 按请求行前缀判断路径，返回不同 HTML 文件；记得拼 `Content-Length`
- 单线程致命伤：一个慢请求（`thread::sleep`）会堵死所有后续连接
- naive 方案是 `thread::spawn` 每连接一线程，但不能无限开
- 明天要实现固定大小的线程池来优雅解决并发问题

## 动手任务

1. 把今天的单线程服务器跑起来，按上面的步骤亲手复现“慢请求堵死一切”的现象。
2. 把 `handle_connection` 改成 `thread::spawn` 版，再做一次同样的实验，确认第二个请求不再被堵。
3. 思考：如果同时开 100 个浏览器标签访问 `/sleep`，`thread::spawn` 版会发生什么？（提示：Windows 线程数是有限的。）

> 提示：`b"GET / HTTP/1.1\r\n"` 是字节串，用来和 `buffer` 的字节做 `starts_with` 比较，避免字符串转义麻烦。

**配合阅读**：[官方书·20.1 构建单线程 Web 服务器](https://rustwiki.org/zh-CN/book/ch20-01-single-threaded.html)
