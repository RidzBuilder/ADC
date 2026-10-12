# P1 manual acceptance checklist

Run this checklist manually for one complete M0 vertical slice. Do not mark PASS without evidence.

- [ ] Intake captures business context, audit scope, missing information, and target-site authorization.
- [ ] Research findings include public source URLs and observation dates.
- [ ] Audit findings distinguish verified facts, inferences, confidence, and unknowns.
- [ ] Roadmap priorities are linked to findings and include observable completion signals.
- [ ] Report is in Indonesian and includes sources and limitations.
- [ ] Evaluation records PASS/FAIL/BLOCKED for each check.
- [ ] Forced-failure test: remove a required input and verify the workflow reports BLOCKED/FAIL rather than inventing a result.
- [ ] Trace is written and read back successfully.
- [ ] No deployment, client message, secret, or paid action occurs without explicit approval.

Exit condition: one complete M0 run passes with trace evidence. Only then consider M1 connector-assisted mode.
