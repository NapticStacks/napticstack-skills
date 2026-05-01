---
name: naptic-review
description: >
  Use when starting a code-review session for NapticStacks PRs. Surfaces
  PRs from the Jordan Approval Queue (open PRs across NapticStacks org
  with review-requested:maydaycyber) older than the staleness threshold,
  then dispatches to gstack `/review`. Triggers on "naptic review",
  "review the queue", "what PRs are aging", "approval queue".
argument-hint: "[--threshold-days=7] [--reviewer=<login>]"
---

# naptic-review

Naptic-flavored wrapper around gstack `/review`. Surfaces aging open PRs across the NapticStacks org that are review-requested to the configured reviewer (default `maydaycyber`), then hands off to gstack `/review` for the actual diff analysis. The wrapper announces the queue; the reviewer picks the PR; gstack reviews it.

---

## Process

### Step 1 — Query the Jordan Approval Queue

Default reviewer is `maydaycyber`. Override via `--reviewer=<login>` (substitute into the `q` string).

```bash
REVIEWER="${ARG_REVIEWER:-maydaycyber}"
gh api -X GET search/issues \
  -f q="is:pr is:open org:NapticStacks review-requested:${REVIEWER}" \
  --jq '.items[] | {title, html_url, repo: .repository_url, number, created_at, updated_at}'
```

Note: the GitHub search API returns `repository_url` as an API URL like `https://api.github.com/repos/NapticStacks/social-media-manager`. Convert to display form (`owner/repo`) by stripping the `https://api.github.com/repos/` prefix before printing.

### Step 2 — Filter by staleness

Default threshold is 7 days. Override via `--threshold-days=<n>`. Use `updated_at` (not `created_at`) — last activity matters more than open age, since a PR with new commits or comments is healthier than a PR that has sat untouched.

Compute `now - updated_at` in days for each PR. Filter to PRs where age >= threshold.

Print:

- **If N stale PRs (N >= 1):**
  ```
  You have <N> aging PR(s):
  - <title> — <owner/repo>#<number> — <age> days since update — <html_url>
  - <title> — <owner/repo>#<number> — <age> days since update — <html_url>
  ...
  ```

- **If N == 0:**
  ```
  Approval queue is clean — no PRs older than <threshold> days.
  ```
  Then exit. Do NOT invoke `/review`.

### Step 3 — Dispatch to gstack `/review`

If at least one stale PR was surfaced, invoke `/review` via the Skill tool with no args. The reviewer chooses which PR from the surfaced list — do NOT auto-pick the oldest or any other heuristic; the human reviewer decides.

---

## References

- Saved-search URL (Jordan Approval Queue, team-view): https://github.com/pulls?q=is%3Apr+is%3Aopen+org%3ANapticStacks+review-requested%3Amaydaycyber
- Underlying gstack skill: `~/.claude/skills/review/SKILL.md`
- Cross-reference: `UNF_PHASE_MAP.md` at the repo root
