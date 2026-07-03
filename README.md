# Superboost v4.0

A personal operating layer for [Claude Code](https://claude.com/claude-code) by ISYNCSO — a RAM/model HUD, real safety guardrails, and lean agent-orchestration guidance, wired into `~/.claude`.

> **v4 in one line:** enforce with hooks, don't narrate with ceremony. Lean on the native harness (Workflow tool, agent teams) instead of hand-rolled orchestration.

## What's in here

| File | Role |
|------|------|
| `CLAUDE.md` | Global behavior: Auto-Router (solo-default), Model Tiering (alias-only), safety & orchestration guidance. Loaded into every session. |
| `settings.json` | Hook bindings, plugins, `defaultMode: auto` (made safe by `safety-guard.sh`). |
| `hooks/superboost-banner.sh` | SessionStart install self-test. **Silent on success**; surfaces only problems. |
| `hooks/superboost-statusline.sh` | Statusline HUD (RAM bar, GB free, capacity hint, model, cost). **Pure ASCII.** |
| `hooks/safety-guard.sh` | **PreToolUse deny hook** (Bash/Write/Edit): blocks `rm -rf /`~, disk format, fork bombs, `git push --force`, secret exfil, and edits to calculator-locked files. |
| `hooks/resource-guard.sh` | PreToolUse spawn guard — blocks agent/team/workflow spawns only when RAM is genuinely too low. Exit 2 = block. |
| `hooks/resource-check.sh` | RAM/CPU/pressure probe → JSON. |
| `hooks/ram-monitor.sh` | PostToolUse RAM logger — sampled + rotated. |
| `hooks/gitnexus-refresh.sh` | SessionStart index-freshness report — cwd-guarded, no auto-exec. |
| `hooks/bless-hooks.sh` | Re-seed sha256 checksums in `superboost-version.json` after editing a hook. |
| `superboost-version.json` | Version + alias-only model tiers + tracked-hook checksums + changelog. |

## Design principles

1. **Safety is enforced, not narrated.** `defaultMode: auto` is safe because `safety-guard.sh` actually blocks the catastrophic cases in a PreToolUse hook — not because a checklist asks the model to be careful. The guard is deliberately conservative (ordinary `git push`, deploys, and SQL are allowed).
2. **Defer orchestration to the harness.** Use the native Workflow tool (concurrency cap, shared token budget, resume, live `/workflows` UI) instead of hand-rolled waves/zones/progress-bars.
3. **No stale model pins.** Model tiers are alias-only (`opus`/`sonnet`/`haiku`) — the Agent tool can't pin a minor version anyway.
4. **Observable, not noisy.** The statusline HUD stays; the mandatory session banner and per-agent ceremony are gone.

## Operating

```bash
# After editing any hook, re-seed checksums (silences the drift warning):
~/.claude/hooks/bless-hooks.sh

# Manual resource read:
~/.claude/hooks/resource-check.sh            # human
~/.claude/hooks/resource-check.sh --quiet    # JSON

# Run the install self-test on demand:
~/.claude/hooks/superboost-banner.sh
```

## Secrets

No secrets live in this repo. Store credentials in the macOS keychain and reference them by name, e.g.:

```bash
security add-generic-password -a "$USER" -s isyncso-supabase-mgmt-token -w   # add (prompts for value)
security find-generic-password -s isyncso-supabase-mgmt-token -w             # retrieve
```

The `.gitignore` uses a **whitelist** model: everything is ignored except the authored config listed above, so session transcripts, logs, caches, `.env`, and `settings.local.json` can never be committed.

---
*ISYNCSO · github.com/frogody/superboost-v4*
