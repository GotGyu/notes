# chrono

## NaiveDate

仅表示一个日历日期，不包括时间或时区信息

## NaiveDateTime

一个“天真的”日期和时间，即不包含任何时区信息的日期和时间。这种类型仅仅表示一个日历日期和一天中的时间，而没有任何关于地理或政治时区的数据。

# futures_util

用于处理 `Futures`, `Streams`, `Sinks` 以及 `AsyncRead` 和 `AsyncWrite` 特性的组合和工具

## 使用场景

## 添加依赖

```rust
[dependencies]
futures-util = "0.3"
```

## mod

### /

- trait 
  - `SinkExt`：所有实现了 `Sink` （表示一个可写入的流）的类型都实现了 `StreamExt`，即 `Sinks` 的扩展trait
  -  `StreamExt`：所有实现了 `Stream` 的类型都实现了 `StreamExt`
    - `next()`：返回 `Option<T::Item>`，`Some(item)` 表示成功接收到一个流中的新数据 `item`，`None` 表示流结束
  -  `TryStreamExt`：所有实现了 `TryStream` 的类型都实现了 `TryStreamExt`

### stream

异步流

- trait
  -  `FusedStream`：一个 `Stream`，用于跟踪是否不再轮询底层数据流
- struct
  - `SplitSink`：split pair 的 `Sink` 部分
  - `SplitStream`：split pair 的 `Stream` 部分

# lazy_static

一个用于在Rust中声明懒惰静态变量的宏。使用它可以创建需要在运行时执行代码才能初始化的静态变量，包括任何需要堆分配的静态变量（如向量、哈希表）。

## 使用场景

Rust 的 `static` 初始化必须是常量表达式，即在编译时就能确定的值。然而有些静态变量的初始化可能涉及复杂的逻辑、运行时计算或依赖外部资源，这些操作无法在编译时完成。`lazy_static` 宏提供了一种机制，允许在第一次访问静态变量时才进行初始化。

主要用于延迟初始化复杂的静态变量（如 `Mutex`, `HashMap` 、层层嵌套的变量等），这些变量可能需要在运行时进行初始化

## 添加依赖

```rust
[dependencies]
lazy_static = "1.5.0"
```

## 实现原理

基于 `Once` 类型，这是一个同步原语，确保某个操作只会执行一次，内部使用了一个原子变量和一个条件变量来实现线程安全，从而确保初始化逻辑只会执行一次。当第一次访问静态变量时，宏会调用初始化逻辑，并将结果存储在静态变量中，后续访问时，直接返回已经初始化的值

# reqwest

## Client

一个结构体，表达一个异步客户端，可以发送请求。客户端可以调整很多配置值，默认设置为最常用的，要配置需要使用 `Client::builder()`。`Client` 内部有一个连接池，所以建议只创建一个 `Client` 并重复使用它。

# serde_json

## from_str

可以将 `JSON` 数据字符串解析为 `Value` 值

```rust
let data = r#"
    {
        "name": "John",
        "age": 30,
        "city": "New York"
    }"#;

let v: Value = serde_json::from_str(data)?;
```

## Value

`enum` 类型结构体，可以用于表示任何有效 `JSON` 数据，包括对象、数组、字符串、数字、布尔值和 `null`。

# tokio

一个用于编写可靠网络应用且不牺牲性能的运行时，是一个事件驱动的非阻塞 `I/O` 平台，用于编写异步应用程序，主要提供几个组件：

- 处理异步任务的工具，包括同步原语和通道以及超时、休眠和间隔；
- 用于执行异步 I/O 的 API，包括 TCP 和 UDP 套接字、文件系统操作以及进程和信号管理；
- 用于执行异步代码的运行时，包括一个任务调度器、一个由操作系统事件队列（epoll、kqueue、IOCP 等）支持的 I/O 驱动程序和一个高性能定时器。

## 添加依赖

```rust
// 依赖
tokio = { version = "1", features = ["full"] } // full
tokio = { version = "1", features = ["rt", "net"] } // just need tokio::spawn ang TcpStream
```

## mod

### tokio::task

通过 `rt` feature flag 开启，Rust 中的异步程序是以轻量级、无阻塞的执行单元（称为 `tasks`）为基础的，该模块提供了处理 `tasks` 的重要工具：

- `spawn` 函数和 `JoinHandle` 类型，分别用于在 `Tokio` 运行时调度新 `task` 和等待一个已创建的 `task` 的输出
- 在异步任务上下文中运行阻塞操作的函数

### tokio::sync

通过 `sync` feature flag 开启，包含在需要通信或共享数据时使用的同步原语，这些原语包括：

- 用于在任务间发送值的通道如 `oneshot, mpscm watch, broadcast`
- 用于控制对共享、可变值的访问的非阻塞 `Mutex`
- 用于在开始计算前同步多个任务的异步 `Barrier` 类型

#### mpsc

- struct
  - `UnboundedReceiver`：从关联的 `UnboundedSender` 接收值，实例由 `unbounded_channel` 函数创建
  - `UnboundedSender`：向关联的 `UnboundedReceiver` 发送值，实例由 `unbounded_channel` 函数创建

### tokio::time

通过 `time` feature flag 开启，提供了用于跟踪时间和调度的实用程序，其中包括：

- 为`tasks` 设置计时器
- 停止运行、在未来继续运行
- 在一定时间间隔内重复操作的功能。

### tokio::io

提供了异步核心 I/O 原语、和 `AsyncRead`, `AsyncWrite`,`AsyncBufRead` traits。当开启 `io-util` feature flag 时，还提供与这些 traits 相关的combinator和函数。

### tokio::net

通过 `net` feature flag 开启，包含 TCP、UDP 和 Unix 域套接字的非阻塞版本

- struct
  - `TcpListener`：一个TCP套接字服务器，一直监听连接。可以通过 `TcpListenerStream` 转换为 `Stream`
  - `TcpStream`：本地和远程套接字之间的TCP流

### tokio::fs

通过 `fs` feature flag 开启，用于异步执行文件系统 I/O

### tokio::signal

通过 `s` feature flag 开启，用于异步处理 Unix 和 Windows操作系统信号

### tokio::process

通过 `process` feature flag 开启，用于生成和管理子进程

## 使用场景

- `Tokio` 可以通过在每个线程上重复交换当前运行的任务，在几个线程上**并发运行多个任务**。 不过，这种交换只能在 `.await` 点进行，因此长时间不进行 `.await` 的代码会妨碍其他任务的运行。为了解决这个问题，Tokio 提供了两种线程：**核心线程**和**阻塞线程**。
- **核心线程**是所有异步代码运行的地方，`Tokio` 默认会为每个 CPU 核生成一个线程。 可以使用环境变量 `TOKIO_WORKER_THREADS` 来覆盖默认值。

- **阻塞线程**是按需生成的，可用于运行阻塞代码，这会阻塞其他任务的运行，并在一定时间内不使用时保持存活，这可以通过 `thread_keep_alive` 进行配置。 由于 `Tokio` 无法像异步代码那样交换阻塞任务，因此阻塞线程的数量上限非常大。 要生成阻塞任务，应使用 `spawn_blocking` 函数。比如 CPU 密集的任务尤其需要用阻塞线程的方式去处理，使用 `spawn_blocking` 创建一个阻塞的线程去完成相应 CPU 密集任务。

## 实现原理

- tokio 是**协作式**的调度器，如果某个 CPU 密集的异步任务是通过 tokio 创建的，那理论上来说，该异步任务需要跟其它的异步任务交错执行，最终大家都得到了执行，皆大欢喜。但实际情况是，CPU 密集的任务很可能会一直霸占着 CPU，此时 tokio 的调度方式决定了该任务会一直被执行，这意味着，其它的异步任务无法得到执行的机会，最终这些任务都会因为得不到资源而饿死。

# tokio-tungstenite

## mod

### /

- enum
  - `MaybeTlsStream`：可能受 TLS 保护的数据流
- struct
  - `WebSocketStream`：实现 websocket 协议的底层原始流的封装器。一个 `WebSocketStream<S>` 表示握手成功完成，服务器和客户端都已准备好接收和发送数据。`WebSocketStream<S>` 中的信息可通过相应的 `Stream` 和 `Sink` 访问，`<S>` 表示一个传输层协议

### extern mod tungstenite

- enum
  - `Message`：表示 websocket 消息各种形式的枚举

