You are running the AIDLC phase **{{PHASE}}** for repository **{{REPO}}**, issue **#{{ISSUE}}**.

1. Use `gh issue view {{ISSUE}} --repo {{REPO}}` (or GitHub MCP) to load the issue body. Resolve the feature folder from plain-text `` `feature/<slug>/` `` (or parent/sub-feature paths). For **Product Spec**, follow **`blob/<branch>/…`** links in the issue — do not treat relative markdown links like `(feature/…/product-spec.md)` as valid URLs (see `docs/GITHUB-ISSUE-SPEC-LINKS.md` in AI-DLC). Open or create the folder under the repo root and read `product-spec.md` locally.

2. Follow `docs/AIDLC.md` in the workspace for phase definitions.

3. Invoke the matching orchestrator intent:
   - **plan**: use `/plan` for Product Spec per `skills/plan/SKILL.md`.
   - **design**: use `/design` for Tech Spec per `skills/design/SKILL.md` (after Product Spec is approved, same or different person).
   - **build**: use `/build` — deliver an **open PR with green CI** per AIDLC.
   - **review**: use `/review`.
   - **ship**: use `/ship` (Validate + Learn).

4. After a successful phase handoff, do **not** change Project Status in this headless run unless the human documented that — prefer updating issue comment with summary and next steps.

5. Labels: if your team uses `aidlc_work:*`, set `aidlc_work:in_progress` only while you work; when done with this run, either leave status for the human to move the Project card or follow team rules.
