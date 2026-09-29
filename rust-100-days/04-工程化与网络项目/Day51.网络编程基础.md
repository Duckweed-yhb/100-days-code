# Day51 网络编程基础

> 目标：用标准库的 `std::net` 写一个能监听端口、接受 TCP 连接、收发数据的小服务器，理解 TCP 连接的基本模型。

## TCP 是什么

**TCP（传输控制协议）**是网络编程最基础的协议：它在两台机器之间建立一条可靠的、按字节顺序传输的通道。你可以把它想象成一根管子——一端写进去的字节，另一端按顺序读出来，不会丢、不会乱序。

Rust 标准库 `std::net` 提供了两个核心类型：

| 类型 | 角色 | 生活比喻 |
|---|---|---|
| `TcpListener` | 服务器端，绑端口、等连接 | 餐厅门口的叫号机 |
| `TcpStream` | 一条已建立的连接，双向读写 | 一根连接顾客和柜台的电话线 |

> 注意：今天用的是**同步阻塞**的标准库 API。真要做高并发服务器会用 `async`（Day40 学过）或线程池（Day54/55 做），今天先把底子打牢。

## 第一步：绑定端口并接受连接

```rust
use std::net::TcpListener;

fn main() {
    // 绑定本机 127.0.0.1 的 7878 端口，监听连接
    let listener = TcpListener::bind("127.0.0.1:7878")
        .expect("绑定端口失败");

    println!("服务器已启动，监听 127.0.0.1:7878");

    // incoming() 返回一个迭代器：每来一个连接，就产出一个 TcpStream
    for stream in listener.incoming() {
        match stream {
            Ok(stream) => {
                println!("有客户端连上了！");
                // 先简单打印一下，后面再读写数据
            }
            Err(e) => {
                eprintln!("连接出错: {}", e);
            }
        }
    }
}
```

运行下面命令启动服务器：

```powershell
cargo run
```

然后另开一个 PowerShell 窗口，用 `curl` 连一下（或者直接浏览器访问 `http://127.0.0.1:7878`）：

```powershell
curl http://127.0.0.1:7878
```

你会在服务器窗口看到“有客户端连上了！”——但 curl 那边会一直卡住，因为我们还没回数据。

## 第二步：读客户端发来的数据

要读写 `TcpStream`，需要引入 `std::io::prelude::*` 把 `Read`/`Write` trait 的方法带进来：

```rust
use std::io::prelude::*;
use std::net::TcpListener;
use std::net::TcpStream;

fn main() {
    let listener = TcpListener::bind("127.0.0.1:7878").unwrap();
    println!("监听 127.0.0.1:7878");

    for stream in listener.incoming() {
        let mut stream = stream.unwrap();
        handle_connection(&mut stream);
    }
}

fn handle_connection(stream: &mut TcpStream) {
    // 准备一个 1024 字节的缓冲区，用来存读到的数据
    let mut buffer = [0; 1024];
    // read() 把数据读进 buffer，返回读到的字节数；需要 stream 是 mut
    let n = stream.read(&mut buffer).unwrap();

    // 把读到的字节转成字符串打印出来
    let request = String::from_utf8_lossy(&buffer[..n]);
    println!("收到 {} 字节:\n{}", n, request);
}
```

关键点：

- `stream.read(&mut buffer)` 是**阻塞**的：没数据就一直等
- `buffer[..n]` 只取实际读到的前 n 字节，后面是未初始化的零
- `from_utf8_lossy` 把字节转字符串，遇到非法 UTF-8 也不 panic（替换成 �）

## 第三步：回写数据（回声服务器）

现在做一个**回声服务器（echo server）**：客户端发什么，服务器原样发回去：

```rust
use std::io::prelude::*;
use std::net::TcpListener;

fn main() {
    let listener = TcpListener::bind("127.0.0.1:7878").unwrap();
    println!("回声服务器启动: 127.0.0.1:7878");

    for stream in listener.incoming() {
        let mut stream = stream.unwrap();

        let mut buffer = [0; 1024];
        let n = stream.read(&mut buffer).unwrap();

        // 把收到的内容原样写回去
        stream.write_all(&buffer[..n]).unwrap();
        stream.flush().unwrap();   // 强制把内核缓冲区的数据发出去
    }
}
```

验证：服务器开着，另开 PowerShell 用 curl：

```powershell
curl http://127.0.0.1:7878
```

curl 会把它发的 HTTP 请求原样打印回来——因为我们就是把字节回声了。

### write 和 write_all 的区别

- `stream.write(&buf)`：可能只写出去一部分（返回实际写了几个字节），要自己循环补写
- `stream.write_all(&buf)`：内部循环直到全部写完，日常用这个

### flush 为什么必须调

`TcpStream` 有内部缓冲区，`write_all` 写的数据可能还在内核缓冲区里没真正发出去。`flush()` 强制把缓冲刷出去，否则客户端可能一直收不到。

## 客户端：TcpStream::connect

服务器是 `TcpListener::bind`，客户端则是用 `TcpStream::connect` 主动连过去：

```rust
use std::io::prelude::*;
use std::net::TcpStream;

fn main() -> std::io::Result<()> {
    let mut stream = TcpStream::connect("127.0.0.1:7878")?;

    stream.write_all(b"hello server!")?;   // b"..." 是字节串
    stream.flush()?;

    let mut buffer = [0; 1024];
    let n = stream.read(&mut buffer)?;
    println!("服务器回: {}", String::from_utf8_lossy(&buffer[..n]));
    Ok(())
}
```

一个项目里同时跑服务器和客户端：先 `cargo run` 跑 server，再另一个终端 `cargo run --bin client` 跑 client。

## 地址与端口

| 概念 | 说明 |
|---|---|
| `127.0.0.1` | 本机回环地址，只有本机能连 |
| `0.0.0.0` | 监听所有网卡，局域网内其他机器也能连 |
| 端口 0~65535 | 选 1024 以上的（1024 以下是系统保留端口） |

绑定 `0.0.0.0:7878` 后，同一 WiFi 下别人就能用你的 IP 连上来了。

## 今日小结

- `TcpListener::bind("IP:端口")` 监听端口；`.incoming()` 迭代出每个连接
- 每个连接是一个 `TcpStream`，用 `read` 读、`write_all` 写，记得 `flush`
- 要 `use std::io::prelude::*;` 才有 `read`/`write` 方法
- 客户端用 `TcpStream::connect("IP:端口")` 主动连
- `read`/`write` 默认阻塞；单线程服务器一次只能处理一个连接（Day54 解决）

## 动手任务

1. 把上面的回声服务器跑起来，用 curl 连上，观察服务器窗口打印的请求内容。
2. 改一下：服务器收到数据后，不是原样回声，而是回一行固定的文字（比如“已收到你的消息”）。
3. 写一个客户端程序 `TcpStream::connect` 连到你的服务器，发一句“你好”，读回响应并打印。

> 提示：`b"hello"` 是 `&[u8; 5]` 字节串字面量；`String::from_utf8_lossy` 返回的是 `Cow<str>`，直接 `{}` 打印没问题。

**配合阅读**：[std::net 模块文档](https://doc.rust-lang.org/std/net/index.html) · [std::io::prelude 文档](https://doc.rust-lang.org/std/io/prelude/index.html)
