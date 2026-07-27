# Polymarket Market Scanner

基于 Polymarket 官方公开市场数据的只读订单簿扫描器。项目按结果位置准确建立 Yes/No Token 映射，读取真实盘口深度，并在扣除主动成交手续费、滑点缓冲和安全缓冲后计算组合模拟结果。

> **仅用于研究与教学。** 本项目不连接钱包、不要求私钥、不签名、不提交订单、不转移资金，也不绕过地区限制。页面中的全部收益均为公开订单簿快照的模拟计算，不代表真实成交或未来收益，不提供盈利保证。

## 主要功能

- Gamma 市场发现与 keyset 完整分页
- 数组/JSON 字符串兼容解析，以及按位置建立结果 Token 映射
- CLOB 批量订单簿、公开 Market WebSocket 心跳与自动重连
- 全 Decimal 的逐档深度、共同可成交量、费用与缓冲计算
- 手续费无法核验时以 `FEE_UNKNOWN` 关闭机会，不按零处理
- 中文深色控制台、市场详情、机会历史、设置、清洗日志
- SQLite、模拟交易、CSV 导出、Windows 脚本、Docker 与 GitHub Actions

## Windows 使用

需要 Python 3.11 或 3.12：

```bat
install.bat
run.bat
```

打开 <http://127.0.0.1:8000>。完整测试执行 `test.bat`，代码检查执行 `lint.bat`，真实公开接口冒烟测试执行 `live_test.bat`。

## 实际页面截图

![真实公开状态与市场行](docs/images/dashboard.png)

![真实订单簿深度与费用计算](docs/images/market-detail.png)

![没有符合条件机会时的真实空状态](docs/images/opportunities.png)

![基于公开盘口快照的本地模拟记录](docs/images/paper-trades.png)

## Docker

```shell
docker compose up --build
```

## 数据与安全说明

主要数据源只有官方 Gamma API、CLOB 公开只读接口、Market WebSocket 和 Geoblock Endpoint，不抓取网页 HTML，不用第三方报价。默认 CI 不运行 Live Smoke Test。

订单簿机会可能无法真实成交；本项目不承诺盈利。使用者须自行遵守所在地法规与平台规则。实时接口可能变化，本项目不会绕过地区限制。

详细资料见 [API 文档](docs/API_REFERENCE.md)、[计算说明](docs/CALCULATION.md)、[架构](docs/ARCHITECTURE.md) 与 [安全边界](docs/SECURITY_BOUNDARY.md)。

MIT License。欢迎阅读 [贡献指南](CONTRIBUTING.md)。
