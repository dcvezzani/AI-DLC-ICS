# GitHub Issues — linking AIDLC specs from issue bodies

**Audience:** Humans and agents creating or editing **GitHub issue** and **issue comment** bodies in consumer repos using AIDLC.

**Related:** [ISSUE-TRACKER-PORTABILITY.md](ISSUE-TRACKER-PORTABILITY.md) · [work-tracking skill](../skills/work-tracking/SKILL.md) · [`/plan`](../skills/plan/SKILL.md)

---

## The problem

GitHub issue bodies are **not** a git working tree. A markdown link like:

```markdown
[product-spec.md](feature/my-feature/product-spec.md)
```

resolves relative to the **issue URL**, producing a broken link such as:

`https://github.com/<owner>/<repo>/issues/feature/my-feature/product-spec.md`

That path does not exist.

---

## The rule

| Where you write | How to link a spec file |
|-----------------|-------------------------|
| **In-repo markdown** (`feature/…`, PR descriptions on the same branch, README) | Relative paths are fine: `[product-spec.md](../product-spec.md)` |
| **GitHub issue or comment body** | Use a full **`blob/<branch>/<path>`** URL (see below) |
| **Plain path for agents** | Backticks only: `` `feature/<slug>/product-spec.md` `` — no markdown link |

---

## Blob URL pattern

```
https://github.com/<owner>/<repo>/blob/<branch>/<path-to-file>
```

| Placeholder | Meaning |
|-------------|---------|
| `<owner>` / `<repo>` | Consumer repository (e.g. `dcvezzani/brick-counter-coordinator-02`) |
| `<branch>` | Git branch where the spec **exists today** — often an integration branch (`feature/<parent-slug>`) until merged; use `main` after merge |
| `<path-to-file>` | Repo-root path (e.g. `feature/lot-entry-cockpit/product-spec.md`) |

**Example (integration branch):**

```text
https://github.com/dcvezzani/brick-counter-coordinator-02/blob/feature/lot-entry-cockpit/feature/lot-entry-cockpit/sub-features/filterable-picker/product-spec.md
```

**Example (after merge to main):**

```text
https://github.com/dcvezzani/brick-counter-coordinator-02/blob/main/feature/lot-entry-cockpit/product-spec.md
```

Consumer repos may document a default blob branch in **`AGENTS.md` → Issue tracker (AIDLC) → Notes**.

---

## Parent Feature issue body (template)

Replace placeholders before `gh issue create`.

```markdown
## Summary

<One paragraph — what and why>

## Feature folder

`feature/<slug>/`

- Product Spec: [product-spec.md](https://github.com/<owner>/<repo>/blob/<branch>/feature/<slug>/product-spec.md)

## AIDLC phase

- [ ] Plan (Product Spec)
- [ ] Design (Tech Spec)
- [ ] Build
- [ ] Review
- [ ] Ship / Validate
- [ ] Learn
```

---

## Child work item issue body (template)

For parent epic **#PARENT** with child slug under `feature/<parent>/sub-features/<child>/` or top-level `feature/<child>/`.

```markdown
## Summary

<Child scope in one or two sentences>

## Parent

Part of [#PARENT <parent title>](https://github.com/<owner>/<repo>/issues/<PARENT>).

## Feature folder

`feature/<parent>/sub-features/<child>/`

- Product Spec: [product-spec.md](https://github.com/<owner>/<repo>/blob/<branch>/feature/<parent>/sub-features/<child>/product-spec.md)

## Dependencies

<Other child slugs or —>

## AIDLC phase

- [x] Plan (Product Spec draft)
- [ ] Design (Tech Spec)
- [ ] Build
- [ ] Review
- [ ] Ship / Validate
- [ ] Learn
```

---

## `gh issue create` example

```bash
OWNER=dcvezzani
REPO=brick-counter-coordinator-02
BRANCH=feature/lot-entry-cockpit
SLUG=filterable-picker
PARENT=10

gh issue create --repo "$OWNER/$REPO" \
  --title "#${PARENT} · Filterable picker" \
  --body "$(cat <<EOF
## Summary

Port shared filterable dropdown primitive.

## Parent

Part of [#${PARENT}](https://github.com/${OWNER}/${REPO}/issues/${PARENT}).

## Feature folder

\`feature/lot-entry-cockpit/sub-features/${SLUG}/\`

- Product Spec: [product-spec.md](https://github.com/${OWNER}/${REPO}/blob/${BRANCH}/feature/lot-entry-cockpit/sub-features/${SLUG}/product-spec.md)

## AIDLC phase

- [x] Plan (Product Spec draft)
- [ ] Design (Tech Spec)
- [ ] Build
- [ ] Review
- [ ] Ship / Validate
- [ ] Learn
EOF
)"
```

---

## For headless agents

When loading an issue (`gh issue view`, GitHub MCP):

1. Read **Feature folder** as plain text (`feature/…/`).
2. Follow **Product Spec** only via **`blob/`** URLs — do not treat relative markdown links as file paths.
3. If only a folder path is present, open `feature/<slug>/product-spec.md` in the **local workspace** checkout on the branch named in consumer `AGENTS.md` (or ask the human).

See also [scripts/prompts/aidlc-phase-issue.md](../scripts/prompts/aidlc-phase-issue.md).

---

## Consumer repo hook

Record repo-specific defaults in **`AGENTS.md`**:

```markdown
**Notes:** Spec links in GitHub issue bodies use `blob/<branch>/…` per [GITHUB-ISSUE-SPEC-LINKS.md](https://github.com/dcvezzani/AI-DLC/blob/main/docs/GITHUB-ISSUE-SPEC-LINKS.md). Default blob branch: `main` (or `feature/<integration>` while epic is open).
```

Optional: copy this doc’s patterns into `docs/github-issues.md` in the consumer repo with filled owner/repo/branch.
