# mcp-flightstats

FlightStats MCP — wraps the Cirium FlightStats Flex API (api.flightstats.com/flex)

Part of [Pipeworx](https://pipeworx.io) — an MCP gateway connecting AI agents to 1576+ live data sources.

## Tools

| Tool | Description |
|------|-------------|
| `flightstats_flight_status` | Status of flight AA100 on a date — gate, delay, baggage. Returns operational status (scheduled vs estimated departure/arrival, gate, terminal, baggage claim) for one specific flight identified by carrier IATA code + flight number + date. Example: flightstats_flight_status({ carrier: "AA", flight: "100", year: 2026, month: 7, day: 2, _apiKey: "appId:appKey" }) |
| `flightstats_airport_status` | Departures/arrivals at an airport — a live board of flights leaving from or arriving at an airport on a given date/hour. Example: flightstats_airport_status({ airport: "JFK", type: "dep", year: 2026, month: 7, day: 2, hour: 14, _apiKey: "appId:appKey" }) |
| `flightstats_schedule` | Scheduled flight timetable — the published schedule (departure/arrival airports and times) for a carrier + flight number departing on a date, regardless of live operational status. Example: flightstats_schedule({ carrier: "AA", flight: "100", year: 2026, month: 7, day: 2, _apiKey: "appId:appKey" }) |

## Quick Start

Add to your MCP client (Claude Desktop, Cursor, Windsurf, etc.):

```json
{
  "mcpServers": {
    "flightstats": {
      "url": "https://gateway.pipeworx.io/flightstats/mcp"
    }
  }
}
```

### What this endpoint actually serves

`tools/list` at `https://gateway.pipeworx.io/flightstats/mcp` returns the tools in the table
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

Both URLs reach the same gateway and the same 1576+ data sources. The
only difference is which pack's tools are listed **directly**; `ask_pipeworx`
reaches all of them from either one.

## No MCP client? Call it over HTTP

This pack takes your own API key (`_apiKey`) — we don't front one for it, so there's no curl here that would run without it. Inspect any tool: `GET https://gateway.pipeworx.io/v1/tools/flightstats_flight_status`. Find one: `POST https://gateway.pipeworx.io/v1/tools/search_packs` with `{"query":"..."}`.

## Standalone (no gateway account)

This package also runs as a local stdio MCP server — no Pipeworx account, no
gateway round-trip:

```json
{
  "mcpServers": {
    "flightstats": {
      "command": "npx",
      "args": ["-y", "@pipeworx/mcp-flightstats"]
    }
  }
}
```

Or run it directly to confirm it starts:

```bash
npx -y @pipeworx/mcp-flightstats
```

It speaks MCP over stdin/stdout and answers `initialize`/`tools/list`/`tools/call`
for **only** this pack's tools — none of the shared meta-tools the gateway
connection above adds. Same source, same tools, no ask_pipeworx routing.

## Using with ask_pipeworx

Instead of calling tools directly, you can ask questions in plain English —
this works on the pack endpoint above as well as on the full gateway:

```
ask_pipeworx({ question: "your question about Flightstats data" })
```

The gateway picks the right tool and fills the arguments automatically.

## More

- [Docs and guides](https://pipeworx.io/docs)
- [pipeworx.io](https://pipeworx.io)

## License

MIT
