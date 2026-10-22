# @pipeworx/kr-equities

Korean listed equities: stock-code to issuer and exchange (KOSPI/KOSDAQ) identity, current quotes, daily history, valuation fundamentals, investor-type flows, market indices and market-cap rankings.

Part of [Pipeworx](https://pipeworx.io) — an MCP gateway connecting AI agents to 1866+ live data sources. This is an independent, unofficial integration — not affiliated with, endorsed by, or published by the upstream provider.

## Tools

- `kr_search_stocks(query, limit?)` — resolve a Korean name, English name or 6-character code to the listed issuer, code and market.
- `kr_quote(code)` — price, change, range, volume, market cap, 52-week range, PER/PBR/EPS/dividend yield, foreign ownership, market status.
- `kr_price_history(code, days?)` — daily OHLCV newest first with window performance.
- `kr_investor_flows(code)` — daily net buying by foreign, institutional and individual investors.
- `kr_index(index, days?)` — KOSPI, KOSDAQ or KOSPI200 level and history.
- `kr_top_by_market_cap(market, limit?, page?)` — largest listed companies by market capitalisation.

## Auth

Keyless.

## Data sources

- Korea Exchange, KOSPI and KOSDAQ market data (quotes, index levels, listing identity).
- English-name lookups fall back to Yahoo Finance's listing search, used only to resolve a name to a Korean code.

Codes are 6 characters (digits, or digits plus letters for newer listings). Prices are in KRW and reflect the exchange timestamp in `as_of` (KST); `market_status` says whether the session is open. Names are returned in Korean.

## Quick Start

Add to your MCP client (Claude Desktop, Cursor, Windsurf, etc.):

```json
{
  "mcpServers": {
    "kr-equities": {
      "url": "https://gateway.pipeworx.io/kr-equities/mcp"
    }
  }
}
```

### What this endpoint actually serves

`tools/list` at `https://gateway.pipeworx.io/kr-equities/mcp` returns the tools in the table
above **plus the shared Pipeworx meta-tools** — `ask_pipeworx`,
`discover_tools`, `search_within`, `remember`/`recall` and the rest of the
gateway-wide set. So the tool count you see is larger than this table: a
single-pack endpoint currently lists roughly 30 shared tools alongside the
pack's own. The connection's `initialize` response states its exact scope, and
is the authoritative answer for a given day.

This is deliberate, not multiplexing by accident. The meta-tools are what let a
scoped connection answer a question this pack does not cover — via
`ask_pipeworx`, which routes across the whole catalog — without you adding a
second MCP server. There is currently no way to mount a pack endpoint without
them; if the extra schemas cost you more context than the routing is worth,
connect to the full gateway once rather than to several pack endpoints.

Or connect to the full Pipeworx gateway to get every pack's tools listed
directly, instead of just this one's:

```json
{
  "mcpServers": {
    "pipeworx": {
      "url": "https://gateway.pipeworx.io/mcp"
    }
  }
}
```

Both URLs reach the same gateway and the same 1866+ data sources. The
only difference is which pack's tools are listed **directly**; `ask_pipeworx`
reaches all of them from either one.

## No MCP client? Call it over HTTP

```bash
curl -X POST https://gateway.pipeworx.io/v1/tools/kr_search_stocks \
  -H 'Content-Type: application/json' \
  -d '{"query":"SK hynix"}'
```

No account needed for the first calls. Inspect any tool: `GET https://gateway.pipeworx.io/v1/tools/kr_search_stocks`. Find one: `POST https://gateway.pipeworx.io/v1/tools/search_packs` with `{"query":"..."}`.

## Standalone (no gateway account)

This package also runs as a local stdio MCP server — no Pipeworx account, no
gateway round-trip:

```json
{
  "mcpServers": {
    "kr-equities": {
      "command": "npx",
      "args": ["-y", "@pipeworx/mcp-kr-equities"]
    }
  }
}
```

Or run it directly to confirm it starts:

```bash
npx -y @pipeworx/mcp-kr-equities
```

It speaks MCP over stdin/stdout and answers `initialize`/`tools/list`/`tools/call`
for **only** this pack's tools — none of the shared meta-tools the gateway
connection above adds. Same source, same tools, no ask_pipeworx routing.

## Using with ask_pipeworx

Instead of calling tools directly, you can ask questions in plain English —
this works on the pack endpoint above as well as on the full gateway:

```
ask_pipeworx({ question: "your question about Kr Equities data" })
```

The gateway picks the right tool and fills the arguments automatically.

## More

- [Docs and guides](https://pipeworx.io/docs)
- [pipeworx.io](https://pipeworx.io)

## License

MIT
