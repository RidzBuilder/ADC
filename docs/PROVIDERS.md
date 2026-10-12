# Provider choices and free-first fallbacks

This is the current provider map for P0. Account-specific quotas and current plan limits have not been verified in this run.

| Function | Primary for current phase | Free-first fallback | Phase note |
|---|---|---|---|
| Code, project state, traces | GitHub repository | Local downloadable artifacts only if GitHub access fails | GitHub is source of truth for P0–P2. |
| Public web research | ChatGPT native web search | User-provided source documents | Prefer native search over adding a crawler. |
| Runtime hosting | None selected for P0 | Keep the vertical slice manual until a runtime is needed | No deployment in this phase. |
| Structured database | None for P0–P2 | Versioned JSON/Markdown in GitHub | Reconsider Supabase from P3 onward. |
| Durable background execution | None for P0 | Synchronous/manual workflow | Reconsider only when the core loop needs it. |
| Project tasks | GitHub issues/this repository for the current scaffold | Markdown checklist | Avoid adding another provider without a demonstrated need. |

## Quota handling

Before an action that consumes a metered quota, check the provider's current official limits and available usage if the connected integration exposes it. Warn at 70% and stop at 90% of the relevant quota. If actual usage is unavailable, mark it UNKNOWN and do not imply remaining capacity.

Never place API keys, tokens, passwords, or other secrets in this file.
