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
