# deepscan

A [Claude Code](https://claude.ai/code) skill that performs deep project audits before context compaction or session resumption.

**[Read the docs →](https://qsimeon.github.io/deepscan)**

## The Problem

Claude Code sessions have limited context. When you compact or start fresh, you lose track of what's done, what's half-done, and what the user was trying to achieve. You end up re-exploring the same codebase.

## What It Does

deepscan runs a five-phase audit:

1. **Understand Intent** — reads CLAUDE.md, README, package manifests, git log
2. **Audit Repository** — scans for stale docs, unused files, dead code, inconsistencies
3. **Assess Progress** — categorizes every feature as Complete / In Progress / Pending / Needs Human / Unclear
4. **Generate STATUS.md** — writes a structured handoff document
5. **Present & Discuss** — conversational recap, asks before changing anything

## Quick Start

```bash
# Install
cp -r deepscan ~/.claude/skills/deepscan

# Then just say in any session:
/deepscan
```

## Trigger Phrases

- `/deepscan`
- "review the project"
- "what's the state of things"
- "recap where we are"
- "status check"
- "pre-compact review"

## Also See

- [session-merge](https://qsimeon.github.io/session-merge) — merge split Claude Code sessions

## License

MIT
