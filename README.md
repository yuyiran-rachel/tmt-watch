# tmt-watch

> 美股TMT板块盯盘助手 · Claude Code Plugin

一个 [Claude Code](https://claude.ai/code) 插件，通过 Yahoo Finance MCP 实时监控美股TMT核心标的的价格和成交量异动，一键生成结构化快报。

## 功能特性

- **29支核心标的**：覆盖半导体、AI基础设施、大型科技、SaaS/云软件、网络安全、互联网消费、电信七大子板块
- **三维异动检测**：价格涨跌幅、成交量比值、52周高低位技术信号
- **并行数据拉取**：批量并发调用 Yahoo Finance，秒级完成全池扫描
- **分级输出**：强势异动 → 温和异动 → 成交量异动 → 技术位信号，一目了然
- **灵活参数**：支持自定义阈值、子板块筛选、输出到文件

## 依赖

- **Claude Code** — 需要安装并配置
- **Yahoo Finance MCP Server** — 需要在 Claude Code 中配置 `yfinance` MCP

## 安装

克隆本仓库到 Claude Code 插件目录：

```bash
# macOS / Linux
git clone https://github.com/yuyiran-rachel/tmt-watch.git ~/.claude/plugins/my-plugins/tmt-watch

# Windows (PowerShell)
git clone https://github.com/yuyiran-rachel/tmt-watch.git $env:USERPROFILE\.claude\plugins\my-plugins\tmt-watch
```

重启 Claude Code 或运行 `/plugins reload`。

## 使用方法

在 Claude Code 对话中输入任意触发词：

```
盯盘
TMT行情快报
扫描异动
看看有没有异动
run tmt watch
tmt scan
```

### 附加参数示例

```
盯盘，价格异动阈值改为2%
只看半导体板块
扫描异动，输出到桌面
```

## 输出示例

```
## TMT 盯盘快报 · 02-26 16:30

> 数据来源：Yahoo Finance｜标的池：29支美股TMT

### 价格异动

| 标的 | 公司 | 现价 | 涨跌% | 量比 | 信号 |
|------|------|------|-------|------|------|
| SMCI | 超微电脑 | $44.2 | +7.93% | 1.67x | 🚀📊 |
| NFLX | Netflix | $1,048 | +5.97% | 1.43x | 🚀 |

### 技术位信号

| 标的 | 公司 | 现价 | 涨跌% | 52W高/低 | 位置 | 信号 |
|------|------|------|-------|----------|------|------|
| AMAT | 应用材料 | $193.6 | +4.50% | $194.3 / $130.2 | 99.6% | 🏔️ |
```

## 标的池

完整标的列表见 [`skills/tmt-watch/references/watchlist.md`](skills/tmt-watch/references/watchlist.md)

| 子板块 | 标的数 | 代表标的 |
|--------|--------|----------|
| 半导体 & AI芯片 | 8 | NVDA, AMD, AVGO, MU |
| AI基础设施 | 5 | CRWV, SMCI, DELL, VRT |
| 大型科技 | 6 | MSFT, AAPL, GOOGL, META |
| SaaS & 云软件 | 6 | CRM, NOW, PLTR, SNOW |
| 网络安全 | 2 | CRWD, PANW |
| 互联网 & 消费 | 3 | NFLX, SPOT, UBER |
| 电信 | 1 | TMUS |

## 异动阈值

| 维度 | 阈值 | 信号 |
|------|------|------|
| 价格涨跌 | ≥ +5% | 🚀 强势上涨 |
| 价格涨跌 | +3% ~ +5% | 📈 温和上涨 |
| 价格涨跌 | -3% ~ -5% | 📉 温和下跌 |
| 价格涨跌 | ≤ -5% | 💥 大幅下跌 |
| 成交量比 | ≥ 2.0x | 🔥 成交量暴增 |
| 成交量比 | 1.5x ~ 2.0x | 📊 成交量偏大 |
| 距52W高 | ≤ 2% | 🏔️ 触碰历史高位 |
| 距52W低 | ≤ 2% | 🕳️ 触碰历史低位 |

## 注意事项

- Yahoo Finance 数据为**延迟报价**（非实时），适合小时级监控
- 盘前/盘后时段（PRE/POST）价格波动较大，快报中会注明市场状态
- 本工具仅用于信息参考，不构成投资建议

## 致谢

数据层由 [Alex2Yang97/yahoo-finance-mcp](https://github.com/Alex2Yang97/yahoo-finance-mcp) 提供支持，感谢作者构建并维护了这个优质的 Yahoo Finance MCP Server。

## License

MIT
