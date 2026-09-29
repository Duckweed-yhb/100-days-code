# 第 08 课：Agent 判断层——LLM + 结构化输出

## 学习目标
- 调用 LLM API，把"市场 + 信号"喂给模型
- 用 Pydantic 定义并拿到**结构化**的模型判断（不是闲聊文本）
- 完成 Agent 判断层（参考 TradingAgents 但只做最简一层）

## 前置要求
- 完成课 07（有分析结果）

## 最小知识
- **Agent 判断层在做什么**：分析框架给你"事实信号"（概率变化、异常），LLM 给这些信号一个**综合判断**（如"这个市场值得关注 / 信号可能失真 / 需要人工确认"）。它是"reasoning"层，不是新数据源。
- **结构化输出**：让 LLM 返回固定格式（字段固定、类型固定），Pydantic 来解析校验。示例：
  ```python
  from pydantic import BaseModel
  class MarketVerdict(BaseModel):
      market_id: str
      signal: str          # 如 "watch" / "caution" / "normal"
      confidence: float    # 0-1
      reason: str          # 一句话理由
  ```
- **Prompt**：给模型一段上下文（市场数据 + 信号）+ 明确要求"只输出 JSON，字段为……"，再用 Pydantic 解析。
- **API key**：用环境变量 `OPENAI_API_KEY`（或你用的模型平台），**绝不写进代码/仓库**。

## 动手任务
1. 建 `odds_pulse/agent/schema.py`：定义 `MarketVerdict`（Pydantic）。
2. 建 `odds_pulse/agent/reasoning.py`：
   - `build_prompt(market, signals)` 拼 prompt
   - `call_llm(prompt) -> MarketVerdict`：调 LLM API，返回结构化结果
   - 失败时兜底返回 `MarketVerdict(signal="normal", confidence=0.0, reason="llm unavailable")`
3. 在 `cli.py` 加 `verdict` 子命令：取 top 市场 → 分析 → 调 LLM → 打印结构化判断。
4. 设 `.env`（加入 `.gitignore`），从环境变量读 API key。
5. 跑通一次真实调用，commit + push。

## 验收标准
- [ ] `python -m odds_pulse.cli verdict` 返回 `MarketVerdict` 结构（字段齐全、类型正确）
- [ ] API key 只从环境变量读，仓库里没有任何明文 key
- [ ] LLM 调用失败时程序不崩，有兜底结果
- [ ] 已 push

## 常见坑
- LLM 返回的不是合法 JSON / 字段对不上 → 用 Pydantic 的 `model_validate_json` + `try/except` 兜底，README 里写明"对非结构化输出做容错"。
- 把 key 写死在代码里 → 一旦 push 就泄密了，务必 `.env` + `.gitignore`。
- 每次都调 LLM 太贵/太慢 → 可以只对 top 市场调，或加缓存。

## 预计时长
约 2.5 小时
