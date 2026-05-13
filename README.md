# ⚠️ ARCHIVED — moved to `swapsapp/swaps-plugin`

> **This repository has been superseded.** All Claude Code, OpenAI Codex, Gemini CLI, Cursor, Windsurf, and Claude Desktop install paths are consolidated in the canonical repo: [`swapsapp/swaps-plugin`](https://github.com/swapsapp/swaps-plugin).

## Where to go now

| Host | Install command |
|---|---|
| Claude Code | `/plugin marketplace add swapsapp/swaps-plugin` + `/plugin install swaps-mcp@swaps-plugins` |
| Gemini CLI / Cursor / Windsurf / Claude Desktop | Paste the `.mcp.json` snippet from the [new repo's README](https://github.com/swapsapp/swaps-plugin#readme) |
| OpenAI Codex | Clone `swapsapp/swaps-plugin` into `~/plugins/swaps-mcp` |

## Why this exists

This repo was a one-day throwaway scaffold during the Road A launch on 2026-05-13. A parallel scaffold (`swapsapp/swaps-codex-plugin`) was published the same day. Both have been consolidated into [`swapsapp/swaps-plugin`](https://github.com/swapsapp/swaps-plugin) so there is one source of truth across all hosts.

## Underlying npm package

The MCP server itself is unchanged — still [`@agent.swaps/mcp-server@1.0.1`](https://www.npmjs.com/package/@agent.swaps/mcp-server). Only the plugin wrapper moved.

## Canonical references

- New canonical plugin: [`github.com/swapsapp/swaps-plugin`](https://github.com/swapsapp/swaps-plugin)
- MCP server source: [`github.com/swapsapp/swaps-mcp-server`](https://github.com/swapsapp/swaps-mcp-server)
- Dev page: [`swaps.app/developers/skills/mcp-server`](https://www.swaps.app/developers/skills/mcp-server)
- OpenAPI: [`agent.swaps.app/openapi.json`](https://agent.swaps.app/openapi.json)
