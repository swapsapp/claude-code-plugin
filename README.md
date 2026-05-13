# Swaps — Claude Code Plugin

> Agent-native crypto on-ramp aggregator inside Claude Code.

This plugin wraps [`@agent.swaps/mcp-server`](https://www.npmjs.com/package/@agent.swaps/mcp-server) as a Claude Code plugin. After installation, your Claude Code agent can quote live on-ramps across **Paybis, Transak, Mercuryo, Coinbase Onramp, Bridge, and Partna** — ranked by the same SmartRouter that powers swaps.app — and explain its choice using the inline ranking rationale.

Free, open-source (MIT), no API key.

## Install

```bash
# In Claude Code
/plugin marketplace add swapsapp/claude-code-plugin
/plugin install swaps
```

That's it. On first invocation Claude Code will spawn the MCP server via `npx -y @agent.swaps/mcp-server@1.0.1` and register five tools.

## Try it

After install, ask Claude:

```
Use swaps_quote to find the cheapest way to buy 100 USD of BTC
in the US, then read the inline winner.explain and tell me
why the winning provider beat the others.
```

You'll see a real-time quote with provider, fees, ETA, KYC tier, checkout URL, and the full ranking rationale.

## Tools exposed

| Tool | Purpose |
|---|---|
| `swaps_quote` | Live quote + ranked alternatives, with inline `LLMExplainPayload` rationale. **Primary tool — covers >95% of agent use cases.** |
| `swaps_taxonomy` | 6-axis provider taxonomy snapshot (domain, flow shape, integration style, settlement rail, user constraints, agentic exposure). |
| `swaps_providers_for_corridor` | Provider candidates for a given country + payment method. |
| `swaps_corridors` | Curated top buy/sell corridors. |
| `swaps_explain_choice` | Post-hoc audit by `quoteId`. **Currently degraded** — the inline `winner.explain` in `swaps_quote` responses is the canonical ranking rationale. See `skills/swaps/SKILL.md`. |

## Stable endpoints (host-agnostic)

The MCP server is a thin wrapper around four public HTTP endpoints that any language can call directly:

| Endpoint | Description |
|---|---|
| `https://agent.swaps.app/openapi.json` | OpenAPI 3.1 spec |
| `https://agent.swaps.app/taxonomy.json` | Provider taxonomy snapshot |
| `https://agent.swaps.app/llm/quote` | Live quote |
| `https://agent.swaps.app/llm/routing-explainer` | Post-hoc explainer (currently degraded — see above) |

These URLs are permanent. Breaking changes ship under `/agent/v2/*` while v1 stays live for at least six months.

## Architecture

```
Claude Code agent
       ↓
this plugin (manifest + auto-loaded skill)
       ↓
@agent.swaps/mcp-server@1.0.1 (npm)
       ↓
agent.swaps.app/llm/quote (Vercel rewrite → Supabase Edge Function)
       ↓
SmartRouter (6 provider adapters)
```

The plugin itself is ~5 KB of manifest + skill instructions. The underlying npm package handles the MCP protocol; the live endpoints handle live quote computation.

## Use in other hosts

The same `.mcp.json` works in Cursor, Claude Desktop, Windsurf, OpenAI Codex, and any other MCP-aware host. See [swaps.app/developers/skills/mcp-server](https://www.swaps.app/developers/skills/mcp-server) for host-specific install snippets.

## Versioning

| Component | Version | Where it lives |
|---|---|---|
| Plugin (this repo) | 1.0.0 | `.claude-plugin/plugin.json` |
| MCP server | 1.0.1 | `@agent.swaps/mcp-server` on npm |

The plugin version and server version are independent. A plugin bump (e.g. SKILL.md improvements) does not require a server bump and vice versa.

## Source code

- Plugin (this repo): [`github.com/swapsapp/claude-code-plugin`](https://github.com/swapsapp/claude-code-plugin)
- MCP server: [`github.com/swapsapp/swaps-mcp-server`](https://github.com/swapsapp/swaps-mcp-server)
- Live OpenAPI spec: [`agent.swaps.app/openapi.json`](https://agent.swaps.app/openapi.json)

## License

MIT. See [LICENSE](./LICENSE).

## Regulatory note

Swaps operates through licensed local partners while pursuing its own VASP/MSB license. Quotes returned by this plugin are informational; final amounts at checkout depend on the chosen provider's pricing, KYC outcome, and rail availability.
