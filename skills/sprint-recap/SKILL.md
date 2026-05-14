---
name: sprint-recap
description: Generate a 3-paragraph plain-English recap of what was built in the current session at a 7th-grade reading level. Use proactively at the end of every sprint, push, /ship workflow, /land-and-deploy, or multi-PR session — without being asked. Also use on demand when the user asks to "recap", "summarize the push", "explain what we built", "kid-friendly summary", or "ELI7".
---

# Sprint Recap (7th-Grade Reading Level)

## When to invoke

**Proactively (no user prompt needed):**
- End of any multi-PR session, sprint completion, overnight autonomous push
- After `/ship`, `/land-and-deploy`, `/document-release`, or multi-track work completion
- Right before "standing by" / signing off when meaningful code shipped this session
- Wave 4 / Wave N+1 of any waved-execution plan

**On request:**
- "Recap the sprint", "summary in plain English", "explain what we built", "kid-friendly version", "ELI7", "ELI5 but slightly older", "give me the non-technical version"

**Do NOT invoke for:**
- Single trivial commits (typo fix, dependency bump, README edit)
- Read-only / research sessions where nothing shipped
- Conversations the user explicitly framed as "just looking" / "no need to summarize"

If unsure: lean toward invoking. Cost is low (one minute of generation); benefit is Jordan gets words he can paste to Jenn, investors, or a non-technical teammate immediately.

## The 3-paragraph structure

Strict three paragraphs — not bullets, not five paragraphs, not a single block.

**Paragraph 1 — What got built (concrete + simple).**
Lead with the count + the categories. Example: *"Tonight we shipped five updates to the AI helpers we're building for AER Pilates. Two made the AI smarter. Two made it safer. And one was plumbing — the kind of thing that doesn't change what anyone sees, but it tells the cloud servers how to actually run the new code."*

**Paragraph 2 — How each piece works (gloss jargon, use analogies).**
Walk through each piece in the order they'll matter to the reader, not the order they shipped. Name technical concepts in everyday language: "DynamoDB" → "a table the AI can write to", "CloudWatch alarms" → "automatic uh-oh notifications", "runbook" → "playbook". If a bug fix happened during the push, name it concretely (e.g. *"a tiny but important fix where the old playbook would have accidentally erased the AI's critical settings during an emergency"*) — these moments are where the user learns to trust the process.

**Paragraph 3 — How it fits the grand scheme (anchor to the human).**
Name the human who benefits (Jenn, the on-call engineer, the customer, the investor). Connect tonight's change to the next milestone they'll experience. Example: *"Tonight's changes are the last big chunk before the FAQ agent can stop being a Jordan-only test and start talking to actual customers."* Avoid mentioning sprint numbers, ticket IDs, or technical milestones. Mention the next step in plain English.

## Reading-level rules

| Do | Don't |
|---|---|
| Short sentences (10-18 words avg) | Long sentences with multiple clauses |
| Common words ("playbook", "memory", "uh-oh") | Jargon without gloss ("idempotent", "GSI", "Lambda alias") |
| Active voice ("we added", "it writes down") | Passive voice ("was added", "is written") |
| Concrete nouns ("Jenn", "the FAQ AI", "Sarah") | Abstract nouns ("stakeholders", "operational maturity") |
| Analogies sparingly ("like a memory", "like a playbook") | Mixed metaphors or strained analogies |
| Name the human who benefits | "End users" / "the system" / "stakeholders" |

**Avoid these AI-vocabulary words** (Garry voice rules):
delve, crucial, robust, comprehensive, nuanced, multifaceted, furthermore, moreover, additionally, pivotal, landscape, tapestry, underscore, foster, showcase, intricate, vibrant, fundamental, significant.

**Avoid em dashes** in the prose itself; use periods or commas instead. (Em dashes are fine in PR titles, just not in the recap paragraphs.)

## Output format

Write the recap as its own section using this exact heading shape, with the time-window word matching context (`tonight`, `today`, `this push`, `this sprint`, `this week`):

```markdown
## What got built [tonight | today | this push] — and why it matters

[Paragraph 1]

[Paragraph 2]

[Paragraph 3]
```

No preamble before the heading. No "Here's the recap:" framing. The heading is the framing.

After the three paragraphs, do not add bullet points, callouts, or links. The recap is self-contained.

## Source of truth for what to recap

Pull from the current session's actual ship history, in priority order:

1. PRs merged this session (`gh pr list --state merged --search "merged:>=<session-start>"`)
2. Commits to main since the session started
3. TaskList tasks marked completed this session
4. Files written / modified that shipped (the checkpoint file if one was written)

Do NOT include:
- Tasks attempted but not shipped
- Sub-agent dispatches as their own line items (they're a means, not a what-got-built)
- Process notes ("we used codex review", "we ran 4 worktrees in parallel") — those belong in the technical summary, not the recap

If a checkpoint file (`~/.gstack/projects/<slug>/checkpoints/...-summary.md`) was written this session, read it first — it has the structured list of what shipped. Translate that into the recap, do not summarize the checkpoint itself.

## Examples of grand-scheme framings (paragraph 3)

Anchor to the human who will feel the change:

- *"…so Jenn can spend her hours actually running the studio instead of typing the same answers a hundred times."*
- *"…the next time the on-call engineer's phone buzzes at 3am, the alarm message tells them exactly which dial to turn."*
- *"…so when a customer asks 'do you offer a free class?' they get a friendly, on-brand answer in under a minute, no matter what time of day."*
- *"…this clears the runway for next sprint, when we'll let the AI actually book classes on a client's behalf."*

Avoid framings like:
- "This advances our roadmap toward Q3 milestones." (sprint-speak)
- "Improves system reliability." (jargon)
- "Sets foundation for future enhancements." (vague)

## When the session also had a bug-catch story

If codex review / spec review / tests caught a real bug pre-merge, work it into paragraph 2 as a concrete moment. This is where Jordan's audience learns the process works. Example: *"…we even caught a tiny but important fix where the old playbook would have accidentally erased the AI's critical settings during an emergency."*

Keep it to one sentence. Don't celebrate the process; just name what would have gone wrong.

## After delivering the recap

Stand by. Do not follow up with bullets, "let me know if you want more detail", or a meta-comment about the recap quality. The recap is the closer.
