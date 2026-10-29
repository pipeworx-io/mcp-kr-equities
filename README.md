# @pipeworx/kr-equities

Korean listed equities: stock-code to issuer and exchange (KOSPI/KOSDAQ) identity, current quotes, daily history, valuation fundamentals, investor-type flows, market indices and market-cap rankings.

Part of [Pipeworx](https://pipeworx.io) — an MCP gateway connecting AI agents to 1856+ live data sources. This is an independent, unofficial integration — not affiliated with, endorsed by, or published by the upstream provider.

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

Both URLs reach the same gateway and the same 1856+ data sources. The
only difference is which pack's tools are listed **directly**; `ask_pipeworx`
reaches all of them from either one.

## No MCP client? Call it over HTTP

```bash
curl -X POST https://gateway.pipeworx.io/v1/tools/kr_search_stocks \
  -H 'Content-Type: application/json' \
  -d '{"query":"SK hynix"}'
```

No account needed for the first calls. Inspect any tool: `GET https://gateway.pipeworx.io/v1/tools/kr_search_stocks`. Find one: `POST https://gateway.pipeworx.io/v1/tools/search_packs` with `{"query":"..."}`.

## Run it from npm

`@pipeworx/mcp-kr-equities` is a local stdio bridge to the Pipeworx gateway: your MCP client
starts it with `npx`, and it forwards every call to the same
`https://gateway.pipeworx.io/kr-equities/mcp` endpoint shown above. It needs network access;
there is no offline mode.

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

Without a key it calls anonymously, capped at 50 calls a day. A free account
raises that to 200 a day: get a key at https://pipeworx.io/signup and add
`"env": { "PIPEWORX_API_KEY": "<your key>" }` to the server entry.
The key is sent as a Bearer token, the same way a hosted connection sends it,
so calls are metered and billed exactly as they are there.

It lists **only** this pack's tools by default. Set `PIPEWORX_META_TOOLS=1` to
also list `ask_pipeworx` and the other gateway-wide tools.

Behind an HTTPS proxy: Node's built-in `fetch` does not read `HTTPS_PROXY` by
default. Newer Node releases honour it when `NODE_USE_ENV_PROXY=1` is set; check
your Node version's documentation.

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
