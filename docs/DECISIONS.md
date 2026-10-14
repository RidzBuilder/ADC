# ADC decisions register

This file records current working decisions and unresolved choices. It is not a release gate.

## Working decisions

| ID | Decision | Basis / status |
|---|---|---|
| K1 | Default audience is UMKM unless the user selects another niche. | Architecture decision; revisit with evidence. |
| K2 | Start with R&D and a demonstrable vertical slice. | Current project direction. |
| K3 | Reports are in Indonesian by default. | Working default. |
| K4 | Use a test site that is owned or explicitly authorized. | Safety boundary; target site still needs to be selected. |
| K5 | Initial deliverable is a digital audit plus roadmap. | Working deliverable definition. |
| K6 | Start in M0 manual-guided mode; move to M1 connector-assisted only after one complete M0 run succeeds. | Transition rule. |

## Explicitly unresolved

- Target niche and test site confirmation.
- Exact acceptance metrics for the first M0 run.
- Provider quotas and remaining account-specific usage; these must be checked when a quota-dependent action is planned.

## Change policy

Propose material architecture changes here with evidence and ask Ridz before implementing them. Do not treat reference frameworks as blockers or claim runtime capability without evidence.

## D-001 — Canonical P1 contract reconciliation
- Decision: canonical P1 contracts are cap.consult.intake_brief, cap.digital.site_crawl, cap.consult.digital_audit, cap.consult.report_generate, cap.agency.approval_request, and cap.core.verify_readback.
- Changes: replace legacy intake/audit/report contents; add site-crawl/approval-request/verify-readback; move research and evaluation to archive/contracts; move roadmap to capabilities/p2/roadmap.contract.yaml. No content is silently discarded.
- Rationale: align contracts to ADC architecture and keep roadmap in P2.
- Status: implemented on branch only; pending review.

## D-003 — Firecrawl provider-boundary incident
- Sequence: web fetch failed to establish HTTP status -> re-decision switched to Firecrawl without prior permission (boundary violation) -> HTTP 404; scrapeId 01a1330d-22d6-717f-944f-267523d84aae.
- User retroactively accepts one fetch solely for HTTP-status evidence. No further Firecrawl fetch is authorized without asking.
- Credit OBSERVATION: current balance 978/1000; historical Connected app=32 and Default=5 from 2026-10-01; current-balance period starts 2026-09-26. Attribution of 21-credit pre-fetch delta and URL-level history UNKNOWN; user will inspect dashboard.
- Future rule: provider switching always requires prior direct permission. Already-approved provider can be used within approved scope/quota without per-fetch approval.
- Status: approved for this branch; no merge permission.
