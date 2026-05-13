# Changelog

All notable changes to this Claude Code plugin are documented in this file.

The plugin version is independent from the underlying MCP server version
(`@agent.swaps/mcp-server` on npm). A plugin bump can happen without a server
bump (e.g. SKILL.md improvements) and vice versa.

## [1.0.0] — 2026-05-13

Initial release.

- Wraps `@agent.swaps/mcp-server@1.0.1` as a Claude Code plugin.
- Auto-loads the `swaps` skill so the LLM gets routing and tool-usage guidance
  without explicit invocation.
- Five MCP tools exposed: `swaps_quote`, `swaps_taxonomy`,
  `swaps_providers_for_corridor`, `swaps_explain_choice`, `swaps_corridors`.
- `swaps_explain_choice` is currently degraded (server-side cache is on the
  v1.1.x backlog). The `winner.explain` inline payload in `swaps_quote`
  responses is the canonical ranking rationale and is documented as such in
  `skills/swaps/SKILL.md`.
