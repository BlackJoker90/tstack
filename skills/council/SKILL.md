---
name: council
description: Trigger this if the user says 'council'.
---

# Council

Before an audit, address outstanding PR comments, fix known issues, and run
relevant checks. Give council the updated implementation; do not defer known
fixes until reviews return. If the user wants to discard the implementation
and explore new ideas, skip fixes to code being replaced.

Bring in four delegates to help with the user's request, usually for review
or feedback:

- Codex GPT-6.2 Astra (`gpt-6.2-astra`, `reasoningEffort: xhigh`)
- Claude Fable 5.2 (`claude-fable-5-2`, `effort: xhigh`)
- Claude Opus 5.5 (`claude-opus-5-5`, `effort: xhigh`)
- Antigravity Gemini 4 Neon (`gemini-4-neon`, `thought_level: xhigh`)

In T3 Code, use `orchestrator_capabilities` to resolve the models, then
`delegate_task` to start one delegate per model in parallel. Elsewhere, use
the harness's subagent tools. If a required model or delegation tool is
unavailable, report it instead of substituting.

If the Gemini Neon run fails with a provider error (for example
`NOT_FOUND (code 404)`), start it again once with the same request. If the
retry also fails, report the failure and record `no score` for that round.

Give each delegate the request and relevant context. For review or feedback,
keep their work read-only. Collect their responses and summarize the useful
findings, disagreements, and recommended next steps.

Do not wait idle. When a result arrives, check its findings against the code
at once and decide what to fix while the other delegates run. Do not edit
files while a delegate still reviews the same checkout. If the user's
instructions mark a delegate as non-blocking, do not wait for it; treat a
late result as feedback and do not record its score.

For a code review, ask each delegate for a score out of 10.
Count each full review pass as one round.
A fresh review after fixes starts a new round.

If the work ends in a pull request, put the results in the PR description.
Put this section before the model and harness line at the end:

```markdown
## Council review

Rounds: 2

| Delegate | Round 1 | Round 2 |
| --- | --- | --- |
| GPT Astra | 8/10 | 10/10 |
| Claude Fable | 7/10 | 9/10 |
| Claude Opus | 7/10 | 9/10 |
| Gemini Neon | 8/10 | 9/10 |
```

If a delegate gives no score, write `no score`. Do not invent one.
If the PR is open, edit the description. Do not post a comment for this.
If there is no PR, put the same results in the final reply only.

If you agree with their recommendations, make the changes and request a fresh
review after. Continue this process until all delegates are satisfied with the
outcome.

Do not let repeated reviews result in scope creep. It is your job to fight to
keep scope limited.
