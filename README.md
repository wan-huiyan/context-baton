# context-baton

Two Claude Code skills for passing context across boundaries — so work survives when a single Claude context window doesn't.

| Skill | When to use | Handoff direction |
|---|---|---|
| **[session-handoff](plugins/session-handoff/SKILL.md)** | End of a work session; you want a clean wrap-up a human (or your future self) can resume from. | agent → human |
| **[successor-handoff](plugins/successor-handoff/SKILL.md)** | Mid-run during autonomous work; context is filling up but there's no human in the loop. | agent → agent |

Different problems, same shape: the runner changes, the work doesn't.

## Which skill do I want?

```
Are you wrapping up to hand off to a human?
  → session-handoff
Are you mid-run in autonomous work and context is getting heavy?
  → successor-handoff
```

Using them together: `successor-handoff` keeps an overnight run going through multiple subagent hops; `session-handoff` writes the final deliverable when the run completes and you're ready to brief a human the next morning.

## Installation

**As a marketplace** (gets both skills):

```bash
claude plugin marketplace add wan-huiyan/context-baton
claude plugin install session-handoff@wan-huiyan-context-baton
claude plugin install successor-handoff@wan-huiyan-context-baton
```

**Just one skill** (clone directly):

```bash
# session-handoff only
git clone https://github.com/wan-huiyan/context-baton.git /tmp/context-baton
cp -r /tmp/context-baton/plugins/session-handoff ~/.claude/skills/session-handoff

# successor-handoff only
git clone https://github.com/wan-huiyan/context-baton.git /tmp/context-baton
cp -r /tmp/context-baton/plugins/successor-handoff ~/.claude/skills/successor-handoff
```

## What each skill does

### session-handoff

End-of-session handoff that captures all knowledge, **dispatches session output across the canonical 7-bucket `docs/` taxonomy** (aligned with [memory-hygiene v3.1](https://github.com/wan-huiyan/memory-hygiene)), and prepares paste-ready prompts for the next session. Includes cross-session consolidation when multiple handoffs accumulate, and a mandatory **doc-freshness reverse-lint** verify step that catches stale normative guidance in project docs after this session's lessons.

**v1.8+ follow-up issue emission** — when the future-to-do plan picks up new follow-up items, the skill drafts `gh issue create` commands for them (dry-run preview by default), de-dups against the repo's open issues, and files them so the next session inherits a real breadcrumb instead of plan prose that rots.

**v1.4+ buckets** — session artifacts are routed to `decisions/`, `runbooks/`, `analysis/`, `references/`, `reviews/`, `handoffs/`, `deliverables/` rather than dumped into a single handoff doc. Rich sessions touch 3-5 buckets simultaneously.

```
You: /session-handoff
Claude: [scans git log, dispatches to 7 buckets, runs reverse-lint, drafts follow-up issues, writes next-session prompt]

You: wrap up this session
Claude: [same — triggers on natural language too]
```

### successor-handoff

Mid-run agent-to-agent handoff for long autonomous workflows. Three rules:

1. **Parent orchestrator stays lean** — reads only status files, never query results.
2. **Each track's subagent uses disk as memory** — results land in `state/queries/`, `state/findings/`, etc.
3. **Fresh successor dispatched when pressure rises** — either via subagent self-flag (`status.json: needs_successor`) or parent-observed tool-event threshold. Max 3 hops per track.

No human in the loop, no narrative compression, no wall-clock loss.

```
You: I want to run an overnight research pass across three hypotheses — 8+ hours of BigQuery exploration.
Claude: [reads successor-handoff, proposes state/ layout + per-track thresholds + resume prompt]
```

## Why the pair

Session-handoff is for **end-of-run human delivery**. Successor-handoff is for **mid-run agent continuity**. Trying to use one for the other's job fails:

- Session-handoff mid-run forces lossy narrative compression and a synchronous human-gated restart.
- Successor-handoff at end-of-run leaves you with a filesystem of state instead of a brief a human can read cold.

Use them in their lanes. They compose: a 10-hour autonomous run uses successor-handoff for the 3–4 mid-run hops, then session-handoff at the end when you're ready to tell the human what happened.

## Related skills (not bundled here)

- **[planning-with-files](https://github.com/obra/superpowers)** — the file-as-memory foundation successor-handoff builds on.
- **[subagent-driven-development](https://github.com/obra/superpowers)** — task-level subagent dispatch with review gates; composes with successor-handoff for track-level continuity.
- **[memory-hygiene](https://github.com/wan-huiyan/memory-hygiene)** v3.1+ — source of truth for the 7-bucket `docs/` taxonomy session-handoff dispatches to. Also does deep memory cleanup and full `docs/` taxonomy audits/migrations.
- **[doc-freshness-reverse-lint](https://github.com/wan-huiyan/claude-ecosystem-hygiene/tree/main/plugins/doc-freshness-reverse-lint)** — invoked automatically by session-handoff's Phase 4 verify step to catch stale normative guidance in project docs after lessons/feedback updates. Falls back gracefully if not installed.

## Versions

- **session-handoff** — 1.8.0 (synced from [standalone release](https://github.com/wan-huiyan/session-handoff): bucket-aware dispatch + doc-freshness reverse-lint, aligned with memory-hygiene v3.1 taxonomy; v1.8 adds follow-up issue emission from the future-to-do plan)
- **successor-handoff** — 1.0.0 (initial release, April 2026)

## License

MIT — see [LICENSE](LICENSE).
