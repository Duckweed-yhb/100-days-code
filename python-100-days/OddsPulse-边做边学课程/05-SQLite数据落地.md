# 第 05 课：SQLite 数据落地

## 学习目标
- 用 Python 内置 `sqlite3` 把拉到的市场数据存进本地数据库
- 能查询"某天某市场的历史概率"，为后面"追踪概率变化"打基础

## 前置要求
- 完成课 04（双数据源可用）

## 最小知识
- **为什么存库**：API 只有"当前快照"，我们要"追踪变化"，就必须把每次拉的数据存下来留历史。
- **表结构**：一张 `market_snapshots` 表：`(source, market_id, question, probability, volume, captured_at)`。主键用 `(source, market_id, captured_at)`，避免重复插入。
- **sqlite3 三步**：`connect()` → `cursor.execute(sql, params)` → `commit()`。
- **参数化查询**：用 `?` 占位符传值，绝不用字符串拼接（防 SQL 注入，也是好习惯）。

## 动手任务
1. 建 `odds_pulse/storage/db.py`：
   - `init_db()` 建表（不存在才建）
   - `save_markets(source, markets)` 批量插入
   - `get_history(source, market_id, limit)` 取历史记录
2. 改 `cli.py`：拉数据后自动 `save_markets` 入库（存到 `data/odspulse.db`，加入 `.gitignore`）。
3. 跑两次（间隔一会儿），确认 `market_snapshots` 里同一市场有两条不同 `captured_at` 的记录。
4. 写个查询，打印某市场最新的概率变化（最新值 - 最早值）。
5. commit + push。

## 验收标准
- [ ] `data/odspulse.db` 生成且被 `.gitignore` 忽略（不进仓库）
- [ ] 连续跑两次，库里能看到同一市场多条时间戳记录
- [ ] 能用一条 SELECT 取出某市场历史概率
- [ ] 数据库操作全部用参数化查询（无字符串拼接 SQL）

## 常见坑
- 重复插入报"UNIQUE constraint failed" → 用 `INSERT OR REPLACE` 或先查是否存在。
- `.gitignore` 没写 `data/` → 会把 db 文件推上去，很业余。检查 `.gitignore`。
- 时间戳格式要统一（ISO 字符串或时间戳），否则后面排序会乱。

## 预计时长
约 2 小时
