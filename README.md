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

End-of-session handoff that captures all knowledge, updates documentation, and prepares paste-ready prompts for the next session. Includes cross-session consolidation when multiple handoffs accumulate.

```
You: /session-handoff
Claude: [scans git log, writes handoff doc, updates memory, creates next-session prompt]

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
- **[memory-hygiene](https://github.com/wan-huiyan/memory-hygiene)** — keep persistent `MEMORY.md` tidy after handoffs accumulate.

## Versions

- **session-handoff** — 1.3.0 (unchanged from standalone release)
- **successor-handoff** — 1.0.0 (initial release, April 2026)

## License

MIT — see [LICENSE](LICENSE).
