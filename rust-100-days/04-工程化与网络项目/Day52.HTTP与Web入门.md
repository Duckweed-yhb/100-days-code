# Day52 HTTP 与 Web 入门

> 目标：在 Day51 的 TCP 服务器上，手写一个最小的 HTTP/1.1 响应，让浏览器和 curl 能正常访问，理解 HTTP 报文长什么样。

## HTTP 是什么

Day51 我们写了个回声服务器，但浏览器访问它时其实会报错或卡住——因为**浏览器只懂 HTTP 协议**。HTTP 就是在 TCP 这根“字节管子”上面约定的一套**文本格式**：请求长什么样、响应长什么样，大家都按这个规矩来。

生活比喻：TCP 是电话线，HTTP 是电话里说的“开场白和应答格式”。你接起电话得先说“喂您好”，否则对面不知道你在说啥。

## HTTP 请求报文长什么样

当浏览器访问 `http://127.0.0.1:7878/` 时，它会通过 TCP 连接发来一段纯文本：

```
GET / HTTP/1.1
Host: 127.0.0.1:7878
User-Agent: curl/8.0
Accept: */*

```

拆开看：

| 部分 | 示例 | 含义 |
|---|---|---|
| **请求行** | `GET / HTTP/1.1` | 方法 + 路径 + 协议版本 |
| 请求头 | `Host: ...`、`User-Agent: ...` | 每行一个键值对，描述元信息 |
| 空行 | `\r\n\r\n` | 头结束的标志 |
| 请求体 | （GET 一般没有） | POST 才有 |

**常见 HTTP 方法**：

| 方法 | 用途 |
|---|---|
| `GET` | 取数据（浏览器地址栏输入就是 GET） |
| `POST` | 提交数据 |
| `PUT` / `DELETE` | 更新 / 删除（REST API 常用） |

## HTTP 响应报文长什么样

服务器要回一段同样格式的文本，浏览器才能正确显示。最小响应：

```
HTTP/1.1 200 OK
Content-Type: text/html; charset=utf-8
Content-Length: 13

<h1>Hi</h1>
```

拆开看：

| 部分 | 示例 | 含义 |
|---|---|---|
| **状态行** | `HTTP/1.1 200 OK` | 协议版本 + 状态码 + 原因短语 |
| 响应头 | `Content-Type: ...`、`Content-Length: ...` | 元信息 |
| 空行 | `\r\n\r\n` | 头结束 |
| 响应体 | `<h1>Hi</h1>` | 真正给浏览器看的内容 |

**常见状态码**：

| 状态码 | 含义 | 什么时候用 |
|---|---|---|
| `200 OK` | 成功 | 正常返回页面 |
| `404 Not Found` | 资源不存在 | 访问了不存在的路径 |
| `301/302` | 重定向 | 换地址了 |
| `500 Internal Server Error` | 服务器内部错误 | 代码炸了 |

## 写一个能被浏览器访问的最小服务器

基于 Day51 的回声服务器，把响应换成标准 HTTP 格式：

```rust
use std::io::prelude::*;
use std::net::TcpListener;

fn main() {
    let listener = TcpListener::bind("127.0.0.1:7878").unwrap();
    println!("HTTP 服务器启动: http://127.0.0.1:7878");

    for stream in listener.incoming() {
        let mut stream = stream.unwrap();
        handle_connection(&mut stream);
    }
}

fn handle_connection(stream: &mut TcpStream) {
    let mut buffer = [0; 1024];
    let n = stream.read(&mut buffer).unwrap();
    let request = String::from_utf8_lossy(&buffer[..n]);
    println!("收到请求:\n{}", request);

    // 拼一个 HTTP 响应：状态行 + 响应头 + 空行 + body
    let body = "<h1>你好，Rust！</h1><p>这是我手写的 HTTP 服务器。</p>";
    let response = format!(
        "HTTP/1.1 200 OK\r\nContent-Type: text/html; charset=utf-8\r\nContent-Length: {}\r\n\r\n{}",
        body.len(),
        body
    );

    stream.write_all(response.as_bytes()).unwrap();
    stream.flush().unwrap();
}
```

关键点：

- 每一行结尾是 `\r\n`（回车+换行），这是 HTTP 协议规定的
- 所有响应头之后必须有一个**空行**（`\r\n\r\n`），浏览器靠它区分头和 body
- `Content-Length` 告诉浏览器 body 有多少字节，不然它不知道什么时候读完
- `Content-Type: text/html; charset=utf-8` 告诉浏览器 body 是 HTML 且按 UTF-8 解码

运行下面命令启动：

```powershell
cargo run
```

然后浏览器打开 http://127.0.0.1:7878 ，你应该能看到一个标题“你好，Rust！”。也可以用 curl 看原始报文：

```powershell
curl -v http://127.0.0.1:7878
```

`-v`（verbose）会打印请求和响应的完整报文，特别适合学习协议。

## 按路径返回不同内容

真实服务器要根据路径返回不同页面。请求行里第二部分就是路径，我们截取它判断：

```rust
fn handle_connection(stream: &mut TcpStream) {
    let mut buffer = [0; 1024];
    let n = stream.read(&mut buffer).unwrap();
    let request = String::from_utf8_lossy(&buffer[..n]);

    // 请求行第一行是 "GET /path HTTP/1.1"，取第二列就是路径
    let path = request.lines().next().unwrap_or("");
    println!("请求路径: {}", path);

    let (status_line, body) = if path.starts_with("GET / ") {
        ("HTTP/1.1 200 OK\r\n", "<h1>首页</h1><p>欢迎光临！</p>")
    } else if path.starts_with("GET /about") {
        ("HTTP/1.1 200 OK\r\n", "<h1>关于</h1><p>这是用 Rust 写的。</p>")
    } else {
        ("HTTP/1.1 404 NOT FOUND\r\n", "<h1>404</h1><p>页面不存在</p>")
    };

    let response = format!(
        "{}Content-Type: text/html; charset=utf-8\r\nContent-Length: {}\r\n\r\n{}",
        status_line,
        body.len(),
        body
    );
    stream.write_all(response.as_bytes()).unwrap();
    stream.flush().unwrap();
}
```

现在访问：
- http://127.0.0.1:7878/ → 首页
- http://127.0.0.1:7878/about → 关于页
- http://127.0.0.1:7878/xxx → 404

## 单线程的问题

你会发现：现在这个服务器一次只能处理一个请求。如果一个请求处理得慢（比如故意 `thread::sleep` 几秒），后面所有请求都得排队。这就是 Day54/55 要用多线程和线程池解决的问题。

## 今日小结

- HTTP 是 TCP 之上的文本协议：请求 = 请求行 + 头 + 空行 + body；响应 = 状态行 + 头 + 空行 + body
- 状态行 `HTTP/1.1 200 OK`，常见状态码 200/404/500
- 每行结尾 `\r\n`，头和 body 之间必须有空行；`Content-Length` 告诉浏览器 body 长度
- 从请求行 `GET /path HTTP/1.1` 里解析路径，按路径返回不同响应
- 单线程服务器一次只处理一个请求——下两天解决

## 动手任务

1. 把上面的服务器跑起来，浏览器访问 `/`、`/about`、`/nope`，观察分别返回什么。
2. 加一个 `/hello?name=xxx` 路径：在响应里显示“你好，xxx”（提示：路径后面 `?` 开始是查询字符串，先简单做个 `/hello/xxx` 的路径也行）。
3. 用 `curl -v` 访问你的服务器，把完整的请求和响应报文抄下来，对照今天讲的格式逐行指出哪部分是状态行、哪部分是头、哪部分是 body。

> 提示：`request.lines().next()` 拿第一行请求行；`starts_with` 判断路径前缀。

**配合阅读**：[Mozilla MDN·HTTP 概述](https://developer.mozilla.org/zh-CN/docs/Web/HTTP/Overview) · [std::net::TcpListener](https://doc.rust-lang.org/std/net/struct.TcpListener.html)
