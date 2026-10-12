# ADC — Agency Digital Consultant

ADC is an R&D project for a guided digital-consulting workflow. This repository is the source of truth for P0–P2 project state, contracts, policies, and run traces.

## Current scope

- **Phase:** P0 foundation, preparing the P1 vertical slice.
- **Operating mode:** M0 manual-guided first. Connector-assisted M1 follows only after one complete M0 run succeeds.
- **Language:** Indonesian reports by default.
- **Storage:** Markdown/JSON/YAML in GitHub for P0–P2. Supabase is deferred until P3 or later.
- **Runtime claim:** This repository is a design and foundation scaffold; no running service is claimed yet.

## Repository map

- `docs/` — decisions and provider choices.
- `capabilities/` — initial capability contracts for the P1 vertical slice.
- `policies/` — authority boundaries and quota ledger.
- `schemas/` — run trace schema.
- `state/` — lightweight project state.
- `traces/` — append-only run evidence.
- `ops/` — session log and operational notes.
- `tests/` — manual acceptance and forced-failure checks.

## Safety boundaries

No production deployment, client communication, paid service, secret storage, or other consequential external action is authorized by this scaffold. Ask for explicit approval before such actions. Never put secrets in repository files or chat.

## First validation

Review `docs/DECISIONS.md`, then run the manual-guided P1 checklist in `tests/P1-acceptance.md`. Record the outcome in a new trace; do not claim a capability works until its evidence exists.
