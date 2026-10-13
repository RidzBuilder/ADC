# ADC Architecture reference
Source: project Library file ADC-ARCHITECTURE.md, version 0.1, dated 2026-10-12. This is a concise reference summary, not a verbatim copy.
Canonical P1 contracts:
- cap.consult.intake_brief
- cap.digital.site_crawl
- cap.consult.digital_audit
- cap.consult.report_generate
- cap.agency.approval_request
- cap.core.verify_readback
P1 flow: intake_brief -> site_crawl -> digital_audit -> report_generate -> approval_request.
Roadmap is P2. A complete M0 run with trace is required before M1. Each P1+ phase includes forced failure. P0-P2 artifacts are stored in GitHub files; Supabase is not assumed necessary. Quota warning threshold 70%, stop threshold 90%. External mutation, client communication, deployment, and paid actions require direct approval.
