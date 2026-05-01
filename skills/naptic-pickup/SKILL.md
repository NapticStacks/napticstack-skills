---
name: naptic-pickup
description: >
  Use when starting a NapticStacks coding session and you want to begin
  work on your top weekly priority. Reads your Priority Rank=1 card from
  the Team #1s GitHub Project (org-level project #22), echoes the card
  to you, then dispatches to gstack `/picking-up-work` with the issue
  context pre-loaded so the priority-ladder lookup is anchored on your
  declared #1. Triggers on "naptic pickup", "what's my number one",
  "start my week", "pick up the #1".
argument-hint: "[--owner=<github-login>] [--no-fallback]"
---

# naptic-pickup

Naptic-flavored wrapper around gstack `/picking-up-work`. Anchors the priority ladder on the operator's declared weekly #1 from the org-level Team #1s board (`NapticStacks/projects/22`) before handing off. The wrapper announces today's #1, then dispatches; gstack does the actual scanning, branch creation, and superpower routing.

---

## Process

### Step 1 — Resolve the caller's owner

Default to the currently-authenticated GitHub login. Override via `--owner=<login>` if explicitly passed.

```bash
OWNER="${ARG_OWNER:-$(gh api user --jq '.login')}"
```

If `--owner=<login>` was supplied, use that string verbatim instead.

### Step 2 — Query the Team #1s project

The Team #1s board lives at `NapticStacks/projects/22`. Fetch all items with their field values, then post-filter in `jq` for `Priority Rank == 1` AND `Owner` includes the resolved login.

```bash
gh api graphql -f query='
query($org:String!, $number:Int!) {
  organization(login:$org) {
    projectV2(number:$number) {
      items(first:100) {
        nodes {
          id
          content {
            ... on Issue { title url number repository { nameWithOwner } }
            ... on PullRequest { title url number repository { nameWithOwner } }
          }
          fieldValues(first:20) {
            nodes {
              ... on ProjectV2ItemFieldSingleSelectValue { name field { ... on ProjectV2SingleSelectField { name } } }
              ... on ProjectV2ItemFieldNumberValue { number field { ... on ProjectV2Field { name } } }
              ... on ProjectV2ItemFieldTextValue { text field { ... on ProjectV2Field { name } } }
              ... on ProjectV2ItemFieldUserValue { users(first:5) { nodes { login } } field { ... on ProjectV2Field { name } } }
            }
          }
        }
      }
    }
  }
}' -f org=NapticStacks -F number=22
```

Then filter the returned `nodes[]` to keep items where:

- `fieldValues.nodes[]` contains a `ProjectV2ItemFieldNumberValue` with `field.name == "Priority Rank"` and `number == 1`, AND
- `fieldValues.nodes[]` contains a `ProjectV2ItemFieldUserValue` with `field.name == "Owner"` and at least one `users.nodes[].login == $OWNER`.

If the board uses a single-select Priority Rank instead of a number field, also accept `ProjectV2ItemFieldSingleSelectValue` with `field.name == "Priority Rank"` and `name == "1"`.

### Step 3 — Echo today's #1

Print one line in this exact format:

```
Today's #1: <title> (<repo>#<number>)
```

For example: `Today's #1: Wire LinkedIn quote-card preview (NapticStacks/social-media-manager#42)`.

If the card is open in multiple repos (multiple linked issues/PRs), list them comma-separated:
`Today's #1: <title> (<repo1>#<n1>, <repo2>#<n2>)`.

If the card has no linked issue or PR, show just the title and `(no linked issue)`:
`Today's #1: <title> (no linked issue)`.

### Step 4 — Dispatch to gstack `/picking-up-work`

After the announcement, hand control over to the underlying gstack skill. Invoke `picking-up-work` via the Skill tool, passing the anchor context as args so its priority ladder is already pinned to today's #1:

- The issue/PR URL (from `content.url`).
- The repo (from `content.repository.nameWithOwner`).
- A hint string `anchor=team-1s-rank-1` so gstack knows the rank-1 card is the human-declared anchor.

The wrapper's job ends with the dispatch; gstack does the scanning, branch creation, and superpower routing.

---

## Fallback

If Step 2 returns zero items for the resolved owner, do NOT block. Surface a one-liner:

```
No #1 priority set for <login>; falling back to plain /picking-up-work.
```

Then invoke `/picking-up-work` via the Skill tool with no args.

If `--no-fallback` was passed, exit instead with a non-zero return code and a hint:

```
No #1 priority set for <login>. Set the rank-1 card on the Team #1s board first:
https://github.com/orgs/NapticStacks/projects/22
```

---

## References

- Team #1s board (org project #22): https://github.com/orgs/NapticStacks/projects/22
- Underlying gstack skill: `~/.claude/skills/picking-up-work/SKILL.md`
- Cross-reference: `UNF_PHASE_MAP.md` at the repo root
