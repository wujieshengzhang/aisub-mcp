# AISub MCP server

A free, read-only [Model Context Protocol](https://modelcontextprotocol.io) server that lets AI assistants look up AI subscription prices by country and region, using data from [AISub](https://aisub.yuanwu.ai).

- **URL:** `https://aisub.yuanwu.ai/mcp`
- **Transport:** Streamable HTTP (2026-07-28 protocol, with 2025-era clients supported)
- **Authentication:** none. No API key or account needed.

[中文说明](#中文说明)

## Coverage

- ChatGPT prices on the website checkout and in the App Store
- Claude, Gemini (Google AI), Perplexity and Grok prices in the App Store
- Recorded price changes, and ChatGPT credit packs

## Tools

| Tool | What it does |
|---|---|
| `list_catalog` | Lists products, channels, plan keys, billing intervals, region codes and supported currencies |
| `compare_prices` | Ranks one plan's reference price across regions |
| `get_region_prices` | Shows every plan price of a product in one region, with its rank and its difference from the US price |
| `get_price_history` | Lists recorded price changes for one region, plan and billing interval |
| `get_recent_price_changes` | Lists recent monthly price changes across regions |
| `compare_credits` | Compares credit packs across regions by the price per 100 credits |

Every response states when the prices and the exchange rates were last updated and links to the matching page on AISub. For how reference prices, tax labels and yearly amounts work, see the [pricing methodology](https://aisub.yuanwu.ai/en/methodology).

## Setup

**Claude:** in Settings → Connectors, add a custom connector with the URL above.

**ChatGPT:** turn on developer mode in Settings, create a connector with the URL above and choose no authentication.

**Cursor and similar clients:**

```json
{
  "mcpServers": {
    "aisub": { "url": "https://aisub.yuanwu.ai/mcp" }
  }
}
```

**VS Code** (`mcp.json`):

```json
{
  "servers": {
    "aisub": { "type": "http", "url": "https://aisub.yuanwu.ai/mcp" }
  }
}
```

**Clients that only run local (stdio) servers:**

```json
{
  "mcpServers": {
    "aisub": { "command": "npx", "args": ["-y", "mcp-remote", "https://aisub.yuanwu.ai/mcp"] }
  }
}
```

## Example questions

- Where is ChatGPT Plus cheapest?
- How much is Claude Pro in Japan, and is it cheaper than in the US?
- Which countries changed ChatGPT prices in the last 30 days?

## Limits and privacy

Each source IP can make up to 120 requests per minute. AISub keeps only daily totals of tool calls by tool and client type; it does not store IP addresses, query arguments or responses. See the [privacy policy](https://aisub.yuanwu.ai/en/privacy).

Prices are for reference. Whether you can buy a plan and the final amount are confirmed at the provider's checkout.

## Publishing to the MCP Registry

This repository holds the [`server.json`](server.json) listed in the [official MCP Registry](https://registry.modelcontextprotocol.io). The server code runs on AISub and is not part of this repository.

Maintainers publish with [`mcp-publisher`](https://github.com/modelcontextprotocol/registry), using DNS authentication for `yuanwu.ai`:

1. Generate an Ed25519 key pair and add the TXT record `v=MCPv1; k=ed25519; p=<public key>` at the apex of `yuanwu.ai`. See the registry's [authentication guide](https://github.com/modelcontextprotocol/registry/blob/main/docs/modelcontextprotocol-io/authentication.mdx). Keep the private key out of this repository.
2. Bump `version` in `server.json` and run `mcp-publisher validate`.
3. Run `mcp-publisher login dns --domain yuanwu.ai --private-key <private key>`, then `mcp-publisher publish`.

---

## 中文说明

AISub MCP 服务免费、只读，让 AI 助手直接查询各地区 AI 订阅价格。数据来自 [AISub](https://aisub.yuanwu.ai/zh)。

- **服务地址：** `https://aisub.yuanwu.ai/mcp`
- **传输方式：** Streamable HTTP，兼容 2026-07-28 版协议和 2025 年发布的客户端
- **认证：** 无需 API Key，也无需注册

覆盖 ChatGPT 官网和 App Store 价格，Claude、Gemini、Perplexity、Grok 的 App Store 价格，以及调价记录和 ChatGPT Credits 价格。每次返回都会给出数据更新时间和汇率更新时间，并附上 AISub 对应页面的链接。含税价、免税价和年付金额的口径见[数据来源与计算说明](https://aisub.yuanwu.ai/zh/methodology)。

添加方式和配置示例见上文英文部分，也可查看[中文使用说明页](https://aisub.yuanwu.ai/zh/mcp)。每个来源 IP 每分钟最多 120 次请求；AISub 只按日汇总记录调用次数和客户端类型，不保存 IP 地址、查询参数和返回内容，详见[隐私政策](https://aisub.yuanwu.ai/zh/privacy)。
