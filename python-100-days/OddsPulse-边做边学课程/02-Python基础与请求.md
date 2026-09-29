# 第 02 课：Python 基础（变量/类型/函数/请求）

## 学习目标
- 掌握变量、基础类型、函数、模块/import
- 学会用 `httpx` 发 HTTP 请求、解析 JSON
- 项目增量：能从任意公开 API 拉回 JSON 并打印

## 前置要求
- 完成课 01（venv 能用、包能跑）

## 最小知识
- **变量与类型**：`name = "Polymarket"`、`count = 3`、`price = 0.62`。不写类型，靠值推断。
- **函数**：`def 函数名(参数):` 定义，`return` 返回值。
- **字典** `{}` 与**列表** `[]`：JSON 就是"字典套列表"。
- **import**：`import httpx` 才能用第三方库。
- **httpx 基本用法**：
  ```python
  import httpx
  resp = httpx.get("https://api.example.com/markets", timeout=10)
  data = resp.json()          # 把响应体解析成 Python 字典/列表
  ```
- **print 格式化**：`print(f"概率 {prob:.2f}")`，f-string 里可直接插变量。

## 动手任务
1. **装 httpx**：`pip install httpx`，并写入 `requirements.txt`。
2. 在 `odds_pulse/` 下新建 `probe.py`，用 httpx 请求 Polymarket 的一个公开接口（如 `https://gamma-api.polymarket.com/markets`），把返回的 `data` 用 `print` 打印出来。
3. 观察打印的结构：找到"市场名"和"概率"字段，试着取出一条市场的名字。
4. 把"打印所有市场名"写成一个小函数。
5. 运行 `.venv\Scripts\python -m odds_pulse.probe`，确认能真实拉到数据。
6. 删除 `probe.py`（这是练习，不是交付物），保留心得。

## 验收标准
- [ ] 能从 Polymarket 公开接口拉到 JSON 并打印
- [ ] 写了一个能取市场名的函数
- [ ] 能说出"列表、字典、函数、import"各自是什么

## 常见坑
- 网络/超时 → `httpx.get(..., timeout=10)` 给足超时；公司网络可能要代理。
- `resp.json()` 报错 → 接口可能返回的不是 JSON，先 `print(resp.text)` 看原始内容。
- 缩进错（Python 用缩进区分代码块）→ 函数体一定要缩进 4 空格。

## 预计时长
约 2.5 小时
