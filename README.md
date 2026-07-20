# 🍜 Gachi-Ramen MCP

**A no-auth MCP server for 62,000+ Japanese ramen shops — every prefecture, queryable by any AI agent.**

No API key. No signup. Add the endpoint and start asking.

```
https://ramen.gachi-tokusuru.com/mcp
```

> Registered on the [official MCP Registry](https://registry.modelcontextprotocol.io/) and [Smithery](https://smithery.ai/) (Quality Score 100/100). Dataset methodology archived on Zenodo (DOI: 10.5281/zenodo.21199500).

---

## Quick start

### Claude Desktop

Add to your `claude_desktop_config.json`:

```json
{
  "mcpServers": {
    "gachi-ramen": {
      "command": "npx",
      "args": ["mcp-remote", "https://ramen.gachi-tokusuru.com/mcp"]
    }
  }
}
```

### Any Streamable HTTP client

Point your MCP client directly at `https://ramen.gachi-tokusuru.com/mcp`.

### Try these

- *"Is there a ramen shop on Iriomote Island?"* — yes, two (a jungle island near Taiwan)
- *"What's the northernmost ramen shop in Japan?"* — a diner literally named 最北端 ("Northernmost"), 1.3 km from Cape Soya, 45.5°N
- *"Find tonkotsu ramen within 500m of Ebisu station"*
- *"How many Ramen Shop (ラーメンショップ) franchise locations are in Saitama?"* — 47

## Tools

| Tool | Description |
|---|---|
| `search_ramen` | Search by free text, prefecture/city, ramen style, or lat/lng + radius. Supports Japanese and romanized queries. |
| `get_ramen_shop` | Fetch one shop's full record by its stable ID (`rk_000001` style). |
| `get_ramen_changes` | Monthly change feed — new shops, closure candidates, confirmed closures. |
| `ping` | Health check. |

## What's in the data

Each record includes:

- **Name** — Japanese original + audited romanization
- **Location** — full address, lat/lng, municipality (via GSI reverse geocoding)
- **Nearest station** + distance in meters
- **Style classification** (`keito`) — a 14-value vocabulary (tonkotsu, shoyu, miso, jiro-kei, ...). Unclassified stays `[]` — we don't guess
- **Liveness** — `active` / `closed_candidate` / `closed_confirmed`, maintained by a rolling monthly re-crawl. Verified closures carry an evidence URL. Candidates found open again are restored to active
- **Payment info** where known (cash-only / card / QR)

### Why liveness matters

Stale data kills most hobby datasets. A shop missing from the monthly source for 2 consecutive checks becomes a `closed_candidate`; web-verified closures become `closed_confirmed`. "Reopened after a false alarm" is a real category, and we handle it.

### The romanization problem

Japanese shop names are full of puns, ateji, and non-standard readings that no dictionary handles. We ran a dual-LLM audit on 53,502 non-trivial names — two models romanizing independently, disagreements flagged for review. The naive baseline error rate was **50.4%**. Half of all machine romanizations of Japanese shop names are wrong out of the box.

## Data sources & license

Three layers:

1. **Store facts observed from public web listings** — factual data (names, addresses); no third-party copyright asserted
2. **Japanese municipality open data** — © 各自治体オープンデータ, CC BY (attribution required)
3. **Derived/enrichment values** (style classification, romanization, station distance, reverse-geocoded municipality © 国土地理院 GSI, stable `rk_` IDs) — original work © [gachi-tokusuru.com](https://gachi-tokusuru.com)

Free for individual and agent use. Questions about the data: contact@gachi-tokusuru.com

## Architecture

- **Cloudflare Workers + KV** at the edge — marginal cost per query ≈ zero, which is why it can stay open with no auth
- Source: parallel crawl of ~120,000 public records, deduplicated to 62,000+
- Liveness: rolling monthly re-crawl cycle

Read the full story: **https://ramen.gachi-tokusuru.com/story**

## Links

- 🌐 MCP endpoint: https://ramen.gachi-tokusuru.com/mcp
- 📖 Story: https://ramen.gachi-tokusuru.com/story
- 📦 Smithery: https://smithery.ai/ *(search "gachi-ramen")*
- 🗄️ Zenodo (methodology): https://doi.org/10.5281/zenodo.21199500
- 🐦 X: [@gachidata](https://x.com/gachidata)

---

*Built by a network engineer in Tokyo who posts too many ramen photos on Reddit.* 🍜
