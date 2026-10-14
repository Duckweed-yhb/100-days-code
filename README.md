# 100-Days-Code · 百日编程计划

> 一份"按天推进"的编程自学合集，多语言 100 天路线并行沉淀：**Python、Rust、MoonBit、Java、C、C++、C#、Go**。

本仓库是你（[Duckweed-yhb](https://github.com/Duckweed-yhb)）编程学习路线的统一收纳处，目前包含八套相互独立、按「天」组织的学习资料：

| 子目录 | 内容 | 性质 |
|---|---|---|
| [`python-100-days/`](./python-100-days/) | 骆昊（jackfrued）《Python - 100天从新手到大师》本地整理版 | 第三方开源课程 |
| [`rust-100-days/`](./rust-100-days/) | 面向零基础的 Rust 系统学习路线（Day01~100） | 本仓库原创 |
| [`moonbit-100-days/`](./moonbit-100-days/) | MoonBit 按天教程路线（Day01~，进行中） | 本仓库原创 |
| [`java-100-days/`](./java-100-days/) | Java 大厂学习路线（Day01~100，8 阶段） | 改编自 B 站 Java 路线文案 |
| [`c-100-days/`](./c-100-days/) | C 语言 100 天路线（语法→数据结构→算法→系统底层） | 本仓库原创 |
| [`cpp-100-days/`](./cpp-100-days/) | C++ 100 天路线（基础→OOP→STL→现代 C++→并发） | 本仓库原创 |
| [`csharp-100-days/`](./csharp-100-days/) | C# 100 天路线（基础→.NET→异步→Web 开发） | 本仓库原创 |
| [`go-100-days/`](./go-100-days/) | Go 100 天路线（基础→并发→标准库→Web/微服务） | 本仓库原创 |

---

## 目录结构

```
100-days-code/
├─ LICENSE              # MIT 许可（覆盖原创内容）
├─ README.md            # 本文件
├─ .gitignore
├─ python-100-days/     # 骆昊 Python 课程（第三方）
│   ├─ README.md        # 原作者课程说明
│   ├─ 01-Python语言基础 … 09-项目实战
│   ├─ OddsPulse-边做边学课程 / 公开课 / 番外篇 / res
├─ rust-100-days/       # 原创 Rust 学习路线
│   ├─ README.md / 训练营对齐学习计划.md / 更新日志.md
│   ├─ 01-Rust语言基础 … 06-生态方向与综合
├─ moonbit-100-days/    # 原创 MoonBit 按天教程（进行中）
│   └─ 100days/         # Day01~05 + README
├─ java-100-days/       # 改编自 B 站 Java 路线文案
│   ├─ README.md
│   ├─ 01-JavaSE基础 … 08-项目实战（Day01~100）
├─ c-100-days/          # 原创 C 路线
│   ├─ README.md
│   └─ 01-C语言基础 … 08-综合项目（Day01~100）
├─ cpp-100-days/        # 原创 C++ 路线
│   ├─ README.md
│   └─ 01-C++基础 … 08-综合项目（Day01~100）
├─ csharp-100-days/     # 原创 C# 路线
│   ├─ README.md
│   └─ 01-C#基础 … 08-综合项目（Day01~100）
└─ go-100-days/         # 原创 Go 路线
    ├─ README.md
    └─ 01-Go基础 … 08-综合项目（Day01~100）
```

## 各路线简介

**Python · 100 天从新手到大师** —— 由**骆昊（jackfrued）**维护的经典开源课程，覆盖语言基础、语言应用、编程进阶与 Linux、数据库、Django 实战、网络爬虫、数据分析、机器学习、项目实战九大板块。详见 [`python-100-days/README.md`](./python-100-days/README.md)。

**Rust · 100 天从零基础到系统编程** —— 本仓库原创，按「天」推进，前 60 天打牢 Rust，后 40 天走向系统编程与生态方向，融合官方书、Rustlings 与「开源操作系统训练营」（rCore / no_std）。详见 [`rust-100-days/README.md`](./rust-100-days/README.md)。

**MoonBit · 100 天教程路线** —— 本仓库原创，python-100-days 风格的每日教程（概念→代码→坑→练习→小结），进行中。详见 [`moonbit-100-days/100days/README.md`](./moonbit-100-days/100days/README.md)。

**Java · 100 天大厂路线** —— 改编自一位 26 届、拿过多家大厂 offer 的学长分享的 B 站 Java 学习路线文案，覆盖 JavaSE→MySQL→Web→SpringBoot→Redis→微服务→JVM/JUC→八股→算法→项目，共 8 阶段。详见 [`java-100-days/README.md`](./java-100-days/README.md)。

**C / C++ / C# / Go · 各 100 天路线** —— 本仓库原创的标准学习路线，每门 8 阶段、Day01~100：C 以算法与数据结构为主线；C++ 侧重 OOP/STL/现代 C++/并发；C# 侧重 .NET 生态与 Web 开发；Go 侧重并发模型与工程化。详见各自目录下的 `README.md`。

## 如何使用

```bash
# 克隆本仓库
git clone https://github.com/Duckweed-yhb/100-days-code.git

# 进入任一学习路线
cd python-100-days   # 或 rust-100-days / java-100-days / c-100-days ...
```

建议节奏：**工作日 1~1.5 小时 / 天，周末复盘 + 做项目**。每天完成一篇 markdown，动手敲代码，别只看。

## 版权与许可

- **原创部分**（根目录内容、`rust-100-days/`、`moonbit-100-days/`、`c-100-days/`、`cpp-100-days/`、`csharp-100-days/`、`go-100-days/`）以 **MIT 许可** 发布，详见 [`LICENSE`](./LICENSE)。
- **`python-100-days/`** 为第三方课程，版权归原作者 **骆昊（jackfrued）** 所有，仅作个人学习整理，本仓库的 MIT 许可不覆盖该部分内容。
- **`java-100-days/`** 改编自第三方 B 站 Java 学习路线文案，视频链接与观点归原作者所有，本仓库仅作学习整理；详细来源声明见 [`java-100-days/README.md`](./java-100-days/README.md)。
