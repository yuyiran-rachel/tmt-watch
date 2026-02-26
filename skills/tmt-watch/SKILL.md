---
name: tmt-watch
description: This skill should be used when the user asks to "盯盘", "TMT行情快报", "扫描异动", "TMT板块异动", "看看有没有异动", "美股TMT监控", "run tmt watch", "tmt scan". Fetches real-time Yahoo Finance data for a curated US TMT watchlist and reports price/volume anomalies.
version: 0.1.0
---

# TMT 盯盘助手

美股TMT板块实时异动监控。通过 Yahoo Finance MCP 批量拉取标的数据，按异动强度分级输出快报。

## 执行步骤

### Step 1：并行拉取数据

使用 `get_stock_info` 工具，对以下全部标的发起调用。**尽量并行发起多个工具调用以节省时间**。

完整标的池（共29个），分批并行调用：

**第一批（半导体 & AI芯片）：** NVDA, AMD, AVGO, QCOM, MU, INTC, AMAT, LRCX

**第二批（AI基础设施 & 大型科技）：** CRWV, SMCI, DELL, VRT, HPE, MSFT, AAPL, GOOGL

**第三批（大型科技 + SaaS）：** META, AMZN, TSLA, CRM, NOW, PLTR, SNOW, DDOG

**第四批（其余）：** NET, CRWD, PANW, NFLX, SPOT, UBER, TMUS

每个标的提取以下字段：
- `currentPrice` / `regularMarketPrice`：现价
- `regularMarketChangePercent`：日内涨跌幅%
- `regularMarketVolume`：当日成交量
- `averageVolume`：日均成交量（3个月）
- `fiftyTwoWeekHigh` / `fiftyTwoWeekLow`：52周高低点
- `previousClose`：昨收

### Step 2：计算异动指标

对每个标的计算：

```
涨跌幅    = regularMarketChangePercent
量比      = regularMarketVolume / averageVolume
距52W高   = (fiftyTwoWeekHigh - currentPrice) / fiftyTwoWeekHigh
距52W低   = (currentPrice - fiftyTwoWeekLow) / fiftyTwoWeekLow
```

### Step 3：分级筛选异动标的

按以下规则筛选，一个标的可同时触发多个信号：

**价格异动（任一满足即入列）：**
- `|涨跌幅| ≥ 5%`：强势异动（🚀/💥）
- `3% ≤ |涨跌幅| < 5%`：温和异动（📈/📉）

**成交量异动（任一满足即入列）：**
- `量比 ≥ 2.0`：成交量暴增（🔥）
- `1.5 ≤ 量比 < 2.0`：成交量偏大（📊）

**技术位信号：**
- `距52W高 ≤ 2%`：触碰历史高位（🏔️）
- `距52W低 ≤ 2%`：触碰历史低位（🕳️）

### Step 4：输出快报

按以下格式输出，**只列出有异动的标的**，无异动标的只在末尾汇总列出。

---

```
## TMT 盯盘快报 · {当前时间 MM-DD HH:MM}

> 数据来源：Yahoo Finance｜标的池：29支美股TMT

---

### 价格异动

| 标的 | 公司 | 现价 | 涨跌% | 量比 | 信号 |
|------|------|------|-------|------|------|
| NVDA | 英伟达 | $XXX | +X.X% | X.Xx | 🚀 |

（按涨跌幅绝对值降序排列；无价格异动则写"暂无"）

---

### 成交量异动（无价格异动但量比偏高）

| 标的 | 公司 | 现价 | 涨跌% | 量比 | 信号 |
|------|------|------|-------|------|------|

（无则写"暂无"）

---

### 技术位信号

| 标的 | 公司 | 现价 | 涨跌% | 52W高/低 | 位置 | 信号 |
|------|------|------|-------|----------|------|------|

（无则写"暂无"）

---

### 无异动标的
{ticker1}, {ticker2}, ... （涨跌幅 < 3% 且量比 < 1.5x 的标的逗号分隔）
```

---

## 注意事项

- Yahoo Finance 数据为延迟报价（非实时），适合小时级监控
- 盘前/盘后时段价格波动较大，输出时注明市场状态（`marketState`）
- 若某标的拉取失败，在快报末尾注明"数据获取失败：{ticker}"
- 完整标的池说明见 `references/watchlist.md`

## 附加参数

用户可以在触发时指定：
- **自定义阈值**：如"价格异动阈值改为2%"
- **子板块筛选**：如"只看半导体"
- **输出到文件**：如"输出到桌面"，则将报告写入 `C:\Users\于怡然\Desktop\tmt-{timestamp}.md`

## 额外资源

- **`references/watchlist.md`**：完整标的池、子板块分类、异动阈值说明
