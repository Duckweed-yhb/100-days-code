# Day86 数据处理

> 目标：会用 Rust 做数据处理——读 CSV、用 polars 跑 DataFrame、用 rayon 一行开并行，知道 ndarray 是干什么的。

## Rust 数据处理的全景

你在 Day50 学过 serde（序列化），那是处理「结构化小数据」。真要做数据分析、跑表格、算矩阵，还需要专门的 crate。Rust 这边的分工很清楚：

| 任务 | 用什么 | 大白话 |
|---|---|---|
| 序列化任意结构 | serde + serde_json（Day50） | 结构体 ↔ JSON |
| 读 CSV | csv crate | 逐行读表格文件 |
| 大数据表格分析 | polars | Rust 版 pandas，多核 |
| n 维数组 / 矩阵 | ndarray | Rust 版 numpy |
| 把顺序循环变并行 | rayon | 把 `iter()` 改成 `par_iter()` |

## csv crate：读表格

CSV 是最朴素的数据交换格式。csv crate 帮你逐行读、按列拆：

```toml
[dependencies]
csv = "1"
serde = { version = "1", features = ["derive"] }
```

```rust
use serde::Deserialize;

#[derive(Debug, Deserialize)]
struct Record {
    city: String,
    #[serde(rename = "temperature")]
    temp: f64,
}

fn main() -> Result<(), Box<dyn std::error::Error>> {
    let mut rdr = csv::Reader::from_path("weather.csv")?;
    for result in rdr.deserialize::<Record>() {
        let rec = result?;
        println!("{} -> {} 度", rec.city, rec.temp);
    }
    Ok(())
}
```

## polars：Rust 版 pandas

polars 是用 Rust 写的 DataFrame 库，底层基于 Apache Arrow 列式内存格式，**自动多核并行**，比 pandas 还快。它有两种模式：

- **eager（即时）**：像 pandas 一样，一步步算
- **lazy（惰性）**：先写查询计划，引擎自动优化再执行

```toml
[dependencies]
polars = { version = "0.45", features = ["csv", "lazy"] }
```

```rust
use polars::prelude::*;

fn main() -> PolarsResult<()> {
    // 读 CSV
    let df = CsvReadOptions::default()
        .into_reader_with_filepath("weather.csv")
        .finish()?;

    // 看看长什么样
    println!("{}", df.head(Some(5)));

    // 选列 + 过滤 + 聚合（eager 风格）
    let out = df
        .lazy()
        .select([col("city"), col("temperature")])
        .filter(col("temperature").gt(lit(30.0)))
        .group_by([col("city")])
        .agg([col("temperature").mean()])
        .collect()?;

    println!("{}", out);
    Ok(())
}
```

数据量大、超过内存也不怕——polars 支持流式查询。日常数据分析，polars 基本能平替 pandas。

## ndarray：n 维数组

如果你要做矩阵运算、信号处理、科学计算，用 **ndarray**——它就是 Rust 界的 numpy，提供 `Array1`（向量）、`Array2`（矩阵）等：

```rust
use ndarray::Array2;

let a = Array2::from_shape_vec((2, 3), vec![1.0, 2.0, 3.0, 4.0, 5.0, 6.0])?;
println!("{}", a);
```

ML 推理（Day89）里喂给模型的张量，很多也是 ndarray 风格。

## rayon：一行开并行

这是 Rust 最爽的库之一。你写的是普通迭代器，只要把 `.iter()` 换成 `.par_iter()`，**自动用多核跑**：

```toml
[dependencies]
rayon = "1"
```

```rust
fn main() {
    let nums: Vec<i64> = (1..=1_000_000).collect();

    // 普通顺序版
    let sum_seq: i64 = nums.iter().map(|x| x * x).sum();

    // 并行版：par_iter 自动分块到多核
    let sum_par: i64 = nums.par_iter().map(|x| x * x).sum();

    assert_eq!(sum_seq, sum_par);
    println!("{}", sum_par);
}
```

为什么这么安全？因为 rayon 遵守 Rust 的借用规则——它保证并行时不会有数据竞争。这就是「无畏并发」在数据处理里的体现。

## 今日小结

- serde 处理结构化数据，csv crate 读表格
- polars 是 Rust 版 pandas，列式存储 + 自动多核，支持 lazy 查询优化
- ndarray 是 Rust 版 numpy，做矩阵/科学计算
- rayon 把 `iter()` 改成 `par_iter()` 就自动并行，且无数据竞争
- 选库：小文件 csv，大表格 polars，矩阵 ndarray，并行 rayon

## 动手任务

1. 造一个 1000 行的 CSV（城市、温度），用 csv crate 读出来
2. 用 polars 读同一个 CSV，按城市求平均温度
3. 写一个 1..=1_000_000 平方和，分别用 iter 和 par_iter 跑，对比耗时

**配合阅读**：[Polars 官网](https://pola.rs/)
