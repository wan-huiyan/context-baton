# context-baton — DEPRECATED

> ⚠️ **This marketplace is deprecated as of 2026-05-28.**
>
> - **session-handoff** has moved to its dedicated home: **[wan-huiyan/session-handoff](https://github.com/wan-huiyan/session-handoff)** (current release: **v1.9**).
> - **successor-handoff** is no longer maintained as a paired skill — Claude Code's own context handling has matured to the point where its mid-run agent→agent role is no longer load-bearing for typical workflows.
>
> **What to do:**
>
> ```bash
> # Uninstall the bundle
> claude plugin uninstall session-handoff@wan-huiyan-context-baton
>
> # Install the standalone (current) version
> claude plugin marketplace add wan-huiyan/session-handoff
> claude plugin install session-handoff@wan-huiyan-session-handoff
> ```
>
> The repo is archived for history. No further updates will land here — point all installs at the standalone repo.

---

## Historical context

This marketplace originally bundled two skills for passing context across boundaries:

| Skill | Current home |
|---|---|
| **session-handoff** | → **[wan-huiyan/session-handoff](https://github.com/wan-huiyan/session-handoff)** (actively maintained) |
| **successor-handoff** | This repo (frozen at v1.0.0) |

The pair was useful when Claude Code's mid-run context handling left the agent→agent baton drop to the skill layer. As of mid-2026, that gap has closed enough that the standalone session-handoff repo covers the cases users actually hit.

See [wan-huiyan/session-handoff](https://github.com/wan-huiyan/session-handoff) for the current README, install instructions, version history, and related skills.

## License

MIT — see [LICENSE](LICENSE).
