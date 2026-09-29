# Day89 AI 与机器学习

> 目标：知道 Rust 在 AI/ML 生态里能干什么、不能干什么，认识 candle / burn / ort 三个 crate，并对「生态还年轻」有清醒预期。

## 先说大实话

AI/ML 的训练生态现在是 **Python（PyTorch）的天下**。Rust 在这一块还很年轻——它的定位不是「取代 PyTorch 训练大模型」，而是：

- **推理部署**：把训好的模型拿到 Rust 里跑，追求性能和单文件分发
- **嵌入端/边缘端**：在 WASM、嵌入式、CLI 里跑模型
- **高性能数据管线**：用 Rust 处理喂给模型的数据

别指望用 Rust 从零训练一个大模型——工具链和教程都不如 Python 成熟。

## 三个主流 crate

| crate | 定位 | 训练还是推理 | 大白话 |
|---|---|---|---|
| **candle** | HuggingFace 出的极简 ML 框架 | 可训练可推理 | 轻量、API 像 PyTorch，能跑 LLaMA/Whisper |
| **burn** | 可移植深度学习框架 | 可训练可推理 | 后端可换 CPU/GPU/WASM，类型安全 |
| **ort** | ONNX Runtime 的 Rust 绑定 | **只推理** | 把 Python 训好的 ONNX 模型拿来跑 |

### candle：HuggingFace 的极简框架

candle 主打「极简 + 性能」，API 设计刻意模仿 PyTorch，让你上手快。它能跑 LLaMA、Whisper、YOLO 这些模型：

```rust
use candle_core::{Device, Tensor};

fn main() -> candle_core::Result<()> {
    // 自动选 GPU（有 CUDA 就用，没有回 CPU）
    let device = Device::cuda_if_available(0)?;

    // 创建张量，风格很像 PyTorch
    let a = Tensor::randn(0f32, 1., (2, 3), &device)?;
    let b = Tensor::randn(0f32, 1., (3, 4), &device)?;

    let c = a.matmul(&b)?;  // 矩阵乘法
    println!("{}", c.shape());
    Ok(())
}
```

它拆成了 `candle-core`（张量）、`candle-nn`（神经网络层）、`candle-transformers`（现成 transformer 模型）等子 crate。

### burn：可移植的深度学习框架

burn 的卖点是**后端可插拔**：同一份模型代码，换个后端类型就能跑在 CPU、GPU 甚至 WASM 上。它还支持把 ONNX 模型导入成 Rust 原生代码。

### ort：只做推理，最实用

实际工程里最常见的路径是：**在 Python 里用 PyTorch 训练模型 → 导出成 ONNX 格式 → 用 Rust 的 ort 加载推理**。ort 是微软 ONNX Runtime 的绑定，只做前向推理，不训练：

```rust
// 概念示意：加载一个 ONNX 模型做推理
// use ort::{Session, Value};
//
// let session = Session::builder()?.commit_from_file("model.onnx")?;
// let input = Value::from_array([[1.0_f32, 2.0, 3.0]])?;
// let outputs = session.run(ort::inputs!["input" => input]?)?;
```

## 怎么选

- 想在 Rust 里跑 LLM/Whisper 等现成模型：**candle**
- 想要类型安全、跨后端训练小模型：**burn**
- 已有 Python 训好的模型，只想高性能部署：**ort**

## 今日小结

- Rust 不替代 PyTorch 做训练，强项在推理部署和边缘端
- candle：HuggingFace 极简框架，API 像 PyTorch，能跑 LLaMA/Whisper
- burn：后端可移植（CPU/GPU/WASM）的训练框架
- ort：ONNX Runtime 绑定，只做推理，工程部署最常用
- 生态仍年轻，生产前先评估成熟度和坑

## 动手任务

1. 用 candle 跑一个官方的张量加法示例，确认能编译
2. 了解一下 ONNX 是什么：为什么它能让 PyTorch 模型在 Rust 里跑
3. （可选）找一个公开的小 ONNX 模型，用 ort 加载做一次推理

**配合阅读**：[candle 仓库](https://github.com/huggingface/candle)
