# 100-Days-Code · 百日编程计划

> 一份"按天推进"的编程自学合集：**Python** 从新手到大师 + **Rust** 从零基础到系统编程，两条 100 天路线并行沉淀。

本仓库是你（[Duckweed-yhb](https://github.com/Duckweed-yhb)）编程学习路线的统一收纳处，包含两套相互独立、按「天」组织的学习资料：

| 子目录 | 内容 | 性质 |
|---|---|---|
| [`python-100-days/`](./python-100-days/) | 骆昊（jackfrued）《Python - 100天从新手到大师》本地整理版 | 第三方开源课程 |
| [`rust-100-days/`](./rust-100-days/) | 面向零基础的 Rust 系统学习路线（Day01~100） | 本仓库原创 |

---

## 目录结构

```
100-days-code/
├─ LICENSE              # MIT 许可（覆盖原创内容）
├─ README.md            # 本文件
├─ .gitignore
├─ python-100-days/     # 骆昊 Python 课程（第三方）
│   ├─ README.md        # 原作者课程说明
│   ├─ Python学习资源汇总.md
│   ├─ 更新日志.md
│   ├─ 01-Python语言基础 … 09-项目实战
│   ├─ OddsPulse-边做边学课程
│   ├─ 公开课 / 番外篇 / res
└─ rust-100-days/       # 原创 Rust 学习路线
    ├─ README.md        # 学习路线总览
    ├─ 训练营对齐学习计划.md
    ├─ 更新日志.md
    ├─ 01-Rust语言基础 … 06-生态方向与综合
    └─ res
```

## Python · 100 天从新手到大师

由**骆昊（jackfrued）**维护的经典开源课程，覆盖 Python 语言基础、语言应用、编程进阶与 Linux、数据库、Django 实战、网络爬虫、数据分析、机器学习、项目实战九大板块，另有公开课、番外篇与配套资源。

- 详细目录与说明见 [`python-100-days/README.md`](./python-100-days/README.md)

## Rust · 100 天从零基础到系统编程

本仓库原创的 Rust 学习路线，按「天」推进，前 60 天打牢 Rust，后 40 天走向系统编程与生态方向，融合官方书、Rustlings 与「开源操作系统训练营」（rCore / no_std）。

- 学习路线总览见 [`rust-100-days/README.md`](./rust-100-days/README.md)
- 训练营时间对齐计划见 [`rust-100-days/训练营对齐学习计划.md`](./rust-100-days/训练营对齐学习计划.md)

## 如何使用

```bash
# 克隆本仓库
git clone https://github.com/Duckweed-yhb/100-days-code.git

# 进入任一学习路线
cd python-100-days   # 或 cd rust-100-days
```

建议节奏：**工作日 1~1.5 小时 / 天，周末复盘 + 做项目**。每天完成一篇 markdown，动手敲代码，别只看。

## 版权与许可

- **原创部分**（根目录内容、`rust-100-days/`）以 **MIT 许可** 发布，详见 [`LICENSE`](./LICENSE)。
- **`python-100-days/`** 为第三方课程，版权归原作者 **骆昊（jackfrued）** 所有，仅作个人学习整理，其许可证与使用条款遵循原作者仓库约定，本仓库的 MIT 许可不覆盖该部分内容。
