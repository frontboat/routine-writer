---
name: routine-writer
description: Write routines — scheduled or triggered prompts that iterate over a set of items, optionally take action on them, and return a short structured report. Use when users want to draft a briefing, triage pass, health check, review digest, tracker, fixer, or any recurring prompt over emails, PRs, issues, alerts, CI, or deps.
---

# Routine Writer

A routine is a prompt that fires on a schedule or trigger, iterates over a set of items (inbox entries, open PRs, issues, CI runs, monitoring alerts), optionally takes action on them (opens a PR, posts a comment, applies a fix, files an issue), and produces a short structured report. The report may summarize what the routine found, what it did, or both. Well-written routines share a tight shape; this skill encodes it.

## When to use

Invoke this skill whenever you write or edit a routine, whether the user calls it that or not. Signals: "every morning", "on a schedule", "each time X merges", "digest", "triage", "briefing", "health check", "tracker", "catch me up on", "review my inbox/PRs/issues".

Do not invoke for one-off prompts, single-turn questions, or prompts that don't iterate over a set of items.

## The shape of a routine

Every routine has three parts: title, tagline, body. The body does six moves, in order.

### Part 1 — Title

Short noun phrase, domain + action. No articles, no first-person verbs.

- `Email triage`, `PR review digest`, `System health check`, `Flaky test tracker`, `Briefing`

### Part 2 — Tagline

One line. Describes the outcome, not the mechanism. Ends with a period.

- `Summary of your calendar, emails, and messages.`
- `Categorize and prioritize your inbox, with draft responses for urgent items.`
- `Find tests that pass and fail intermittently across recent CI runs.`

### Part 3 — Body

#### Move 1 — Opening directive

One imperative sentence. Verb + scope. No preamble, no "please", no softening.

- `Generate a briefing to help me catch up. Include:`
- `Review my inbox for new and unread emails. For each email:`
- `Identify flaky tests — tests that both passed and failed on the same code over the past week.`

Pick a specific verb (Review, Identify, Scan, Draft, Perform, Generate) over a generic one (Help, Look at, Check out).

#### Move 2 — Per-item numbered list

When the routine processes a set of units (emails, PRs, issues, tests), open with `For each <thing>:` (or `For each <thing>, report:`) and list the attributes to produce per item. Items are verb-first and each produces a discrete output: a category, a field, a summary, a draft, a flag.

```
For each PR, report:
1. Title and author
2. How long it's been open
3. Review status — approved, changes requested, or awaiting review
4. CI status — passing, failing, or pending
5. Merge conflicts — whether the branch is up to date
```

When the routine produces sections of a single report (not per-item), open with `Include:` or `Report:` and number the sections instead:

```
Generate a briefing to help me catch up. Include:
1. Schedule — List upcoming meetings and events...
2. Important emails — Summarize unread emails...
```

#### Move 3 — Classification schemes (when used)

Enumerate categories inline as a closed set, comma-separated, `or` before the last item. No Oxford comma.

- `Urgent, Action Needed, FYI, or Low Priority`
- `Bug, Feature Request, Improvement, or Question`
- `Critical, High, Medium, or Low`
- `New, Improved, Fixed, or Breaking`

#### Move 4 — Presentation paragraph

A prose paragraph (not a list) specifying how to sort, group, highlight, or format the output.

- `Present results grouped by category, with Urgent items first.`
- `Sort by oldest first.`
- `Present as a ranked table followed by per-test detail.`
- `Prioritize by severity.`

#### Move 5 — Empty-state handling

Always explicit. Never omitted. Names the null case.

- `If the inbox is empty or all caught up, confirm that briefly.`
- `If all systems are healthy, confirm with a brief "All systems nominal" message.`
- `If there are no open PRs, confirm briefly.`

#### Move 6 — Tone/length constraint (optional)

One closing line when output length matters.

- `Keep the briefing concise and scannable.`
- `Keep the tone professional and concise.`
- `Keep drafts short — 2 to 4 sentences. Long release notes don't get read.`

## Secondary patterns

- **Em-dash for inline definition.** When a field name needs values, use `— ` then the enumeration. `Update type — major, minor, or patch`.
- **Branching in prose, not nested bullets.** Use `If X, do Y. Otherwise, do Z.` at the decision point.
- **Skip, don't null-pad.** Tell the routine to omit empty sections rather than writing "nothing to report" under each one.
- **Imperative voice throughout.** Not "you should", "the assistant will", "please". Use direct commands: Categorize, Summarize, Flag, Present, Prioritize, Post.
- **Possessive "my" is fine.** `Review my inbox` is the user's possessive. `You should review the inbox` is soft second-person — avoid it.

## A minimal template

```
<Title>
<Tagline ending in period.>
<Imperative opening + scope>. <For each X: | Include:>
1. <Verb-first attribute or section> — <closed enumeration if classification>
2. ...
3. ...
<Presentation paragraph: sort, group, highlight, format.> <Empty-state sentence.> <Optional tone/length line.>
```

## Common failure modes

- **Preamble.** "This routine will help you..." — cut it. Start with the verb.
- **Open-ended categories.** "Categorize appropriately" — always enumerate the set.
- **Mixed numbering.** Pick one: the list describes attributes for each unit, or it describes sections of one report. Not both.
- **Missing empty-state line.** Leads to "no items found" padding in every section at runtime.
- **Pipeline numbering when items are attributes.** `1. Read the inbox. 2. For each email, categorize...` is a pipeline; `For each email: 1. Categorize... 2. Summarize...` is attribute extraction. Attribute extraction is the common idiom; reach for the pipeline form only when stages truly run in sequence.
- **Explaining the why.** Routines are terminal instructions to an agent with tools, not tutorials. Cut rationale.

## When the routine has real branching

Some routines are multi-stage pipelines rather than per-item extractions — triggered by one event (a PR merge, a CI failure), they run several steps in sequence and sometimes exit early. For these:

- Number the stages as sequential steps, each verb-first.
- Use `If X, <terminal action>. Otherwise, <continue>.` for early exits.
- Stages can include real work, not just reads — apply a fix, open a PR, file an issue, quarantine a test. The report then describes what was done and links to the artifact.
- The terminal action (Post, Open, Submit, Comment) serves as the presentation move — no separate Present/Sort line needed.
- Keep an empty-state sentence at the end.
- End with a length constraint if output size matters.
