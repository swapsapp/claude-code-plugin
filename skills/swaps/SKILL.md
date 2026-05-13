---
name: swaps-mcp
description: Use the Swaps MCP tools for live crypto on-ramp/off-ramp quotes, provider taxonomy, corridor candidates, and curated corridor ordering. The inline explain payload on every swaps_quote response is the source of truth for ranking rationale.
---

# Swaps MCP

Use this skill when the user asks you to reason about Swaps provider quotes, fiat-to-crypto routes, provider choice, country / payment-method support, or any agent-facing Swaps surface.

## Source of truth

- Prefer MCP tools from the `swaps` server over memory or static docs.
- Treat `swaps_quote` as the canonical source of truth for ranked routes.
- **Treat `winner.explain` (and each `alternatives[i].explain`) in the `swaps_quote` response as the canonical ranking rationale.** It is a full `LLMExplainPayload` with `rankingCriteria`, `constraintsActive`, `alternatives[].reasonNotPicked`, `providerTaxonomy`, `sourceOfTruth`, `generatedAt`, and `diagnostic`. You do not need to call a second tool to obtain a justification — it is already there.
- Do not invent provider support, country coverage, payment methods, fees, licenses, limits, or risk verdicts.

## Tools

| Tool | When to use | Notes |
|---|---|---|
| `swaps_quote` | Get a live quote and ranking rationale for a corridor. **Primary tool — covers >95% of agent use cases.** | Response includes `winner.explain` and `alternatives[i].explain` — full ranking rationale is inline. |
| `swaps_taxonomy` | Reason about provider categories (domain, flow shape, integration, settlement rail, agentic exposure). | Static snapshot — cheap, ~365 ms. |
| `swaps_providers_for_corridor` | List provider candidates for a specific country + payment method without running a live quote. | Taxonomy-driven; no rate-limit risk. |
| `swaps_corridors` | List curated top buy/sell corridors. | Useful for discovery / pre-flight. |
| `swaps_explain_choice` | **Currently degraded — do NOT call.** | Designed as post-hoc audit by `quoteId`, but the backend cache is in-memory in a Supabase Edge Function isolate that recycles per request. Calls return `EXPLAIN_EXPIRED` in ~100% of cases. Postgres-backed cache is on the v1.1.x backlog; until then, use the inline `winner.explain` from the original `swaps_quote` response. |

## Operating rules

- State when a result is live-tool-derived versus inferred. Lead with the tool name and the inputs you used.
- For money-flow conclusions, include concrete inputs: `amount`, `from`, `to`, `country`, `side`, and payment `method`. Quote a fresh `swaps_quote` rather than reusing a stale one.
- If `swaps_quote` returns `winner: null` with non-empty `errors`, report the errors verbatim and do not fabricate a winner.
- For ranking justification, read fields directly from `winner.explain` (and each `alternatives[i].explain`). Do not paraphrase the taxonomy — quote `providerTaxonomy.domain` / `providerTaxonomy.integrationStyle` etc. exactly as returned.
- For checkout / redirect claims, the MCP server is informational; the actual user redirect happens through the Swaps app flow. Surface `winner.checkoutUrl` but acknowledge end-state depends on the chosen provider's checkout.
- Do not call `swaps_explain_choice` unless the user explicitly asks for the post-hoc audit endpoint and you have warned them it currently returns `EXPLAIN_EXPIRED`.

## Regulatory wording

When summarising Swaps' regulatory posture, use the exact phrase:
> Swaps operates through licensed local partners while pursuing its own VASP/MSB license.

Never claim Swaps itself is licensed, or that Swaps validates compliance on behalf of providers.
