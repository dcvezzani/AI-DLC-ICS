---
name: git-commit
description: "Use when creating git commits for this repository. Always commit by executing ./git-commit.sh with a full multi-line commit message body; do not run git commit directly."
---

# Git Commit

This skill standardizes commits for this repository by delegating all commits to.  Use the skill’s actual script path when making this call:

- `{codex-resource-dir}/skills/git-commit/git-commit.sh` 

## Rules

- Never use `git commit` directly for normal work.
- Stage only the files for the current gameplan step.
- Pass a complete multi-line commit message to the script.
- Preserve the gameplan commit template sections:
  - `What is the purpose of this change?`
  - `What changed?`

## Command Pattern

- **Critical** <phase/step identifier and title> represents the current gameplan phase and step being implemented, e.g. "GP-01 Phase 1, Step 1: Implement config/env.js"
  - **Critical**: Always start the commit message with the phase and step identifier for traceability.
  - **Critical**: This means do not start the commit message with "Implement "or other verbs; the Phase/Step identifier should be the first thing in the message.
- **Critical**: Do not include the Jira work id number; that should automatically be added by the `./git-commit.sh` script
- Bullets under "What is the purpose of this change?" should explain the rationale and goals of the change.
- Bullets under "What changed?" should summarize the specific code changes made.

```bash
cat <<'EOF' | ./git-commit.sh
Implement <phase/step identifier and title>

What is the purpose of this change?
- <bullet>
- <bullet>
- <bullet>

What changed?
- <bullet>
- <bullet>
- <bullet>
EOF
```

## Notes

- The script determines the Jira ID prefix from the branch context.
- If Jira ID is not present, the script applies its fallback behavior.
