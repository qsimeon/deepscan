---
name: deepscan
author: Quilee Simeon <qsimeon@mit.edu>
url: https://qsimeon.github.io/deepscan
description: >
  Deep scan and review of the entire project before compacting context or resuming work.
  Audits the repo for outdated docs, unused files, dead code, and bloat. Recaps the project's
  original objectives, assesses progress, identifies what's done vs pending, and clarifies
  what needs human action vs what Claude can handle. Use this skill whenever the user says
  things like "deepscan", "review the project", "what's the state of things", "recap where
  we are", "pre-compact review", "clean up before we continue", "what's left to do",
  "audit the repo", "what did we accomplish", "status check", or before any context
  compaction/reset. Also use when starting a new session on an existing project to
  re-orient, or when the user seems confused about what's been done and what hasn't.
---

# Deep Scan — Pre-Compact Project Review

You are performing a thorough review of the project to establish a clear, honest snapshot of where things stand. This serves two purposes: (1) cleaning house so the codebase is tidy, and (2) creating a status document that makes it easy to resume work — whether that's after a compact, in a new session, or by a different person/agent picking up where you left off.

Think of this like a senior engineer doing a end-of-sprint review: walking the repo, checking what shipped, what's stale, and writing up a clear handoff document.

## Procedure

### Phase 1: Understand the Project Intent

Before auditing files, you need to understand *what this project is supposed to be*. Look for intent signals in this order:

1. **CLAUDE.md / AGENTS.md** — Read the project memory files first. They often contain the original goals, architecture decisions, and conventions.
2. **README.md** — The stated purpose of the project.
3. **Package manifests** — `package.json`, `pyproject.toml`, `Cargo.toml`, etc. for project name, description, dependencies.
4. **Git log** — Skim recent commit messages (`git log --oneline -30`) to understand the arc of recent work.
5. **Conversation context** — If there's an active conversation, the chat history itself is a rich source of what the user has been trying to accomplish.
6. **Any planning docs** — Look for files like `TODO.md`, `ROADMAP.md`, `PLAN.md`, `docs/`, `.github/issues`, or similar.

Synthesize these into a clear, concise statement of what the project is and what the high-level goals are. If you're uncertain about the original intent, say so and ask the user to confirm before proceeding.

### Phase 2: Audit the Repository

Scan the repo systematically. For each category below, note specific findings with file paths.

#### 2a. Outdated / Stale Documentation
- README sections that describe features differently than they're currently implemented
- Docs referencing old APIs, removed routes, renamed files, or deprecated dependencies
- Comments in code that describe behavior that has since changed
- CLAUDE.md instructions that no longer match the project state
- Stale inline TODOs or FIXMEs that have already been resolved

#### 2b. Unused / Redundant Files
- Source files not imported or referenced anywhere
- Duplicate files (e.g., `component.tsx` and `component-old.tsx`, `utils.js` and `utils-backup.js`)
- Empty or placeholder files that were never filled in
- Test files for components/modules that no longer exist
- Config files for tools no longer in use (e.g., `.babelrc` when the project moved to SWC)
- Build artifacts or generated files that shouldn't be committed

#### 2c. Code Bloat / Dead Code
- Exported functions or components never imported elsewhere
- Commented-out blocks of code (not explanatory comments — actual dead code)
- Unused dependencies in package manifests
- CSS classes or styles not applied to any element
- Unused variables, imports, or type definitions
- Feature flags or conditional branches for features that have fully shipped or been abandoned

#### 2d. Inconsistencies
- Naming convention mismatches (e.g., some files use camelCase, others kebab-case)
- Mixed patterns that suggest mid-refactor state (half the codebase uses one approach, half another)
- Environment or config values that don't match between files

For this phase, use tools efficiently. `grep`, `find`, and the codebase exploration tools are your friends. Don't just guess — verify. But also don't spend 30 minutes tracing every import; focus on high-signal findings.

### Phase 3: Assess Progress

Create a clear accounting of project status. For each major feature, section, or goal of the project, categorize it:

- **✅ Complete** — Fully implemented and working
- **🔧 In Progress** — Partially done, needs more work
- **⏳ Pending (Claude can do)** — Not started yet, but Claude can implement it
- **👤 Pending (Human needed)** — Requires human action (e.g., providing specific content, images, API keys, design decisions, manual testing, third-party account setup)
- **❓ Unclear** — Can't determine status; needs user clarification

Be specific about *why* something needs human action. "You need to add images" is less helpful than "The hero section references `/public/images/hero.webp` which doesn't exist — you'll need to provide this image."

### Phase 4: Generate the Report

Write a STATUS.md file in the project root (or update it if one exists). Use this structure:

```markdown
# Project Status — {Project Name}
> Last reviewed: {date}
> Reviewed by: Claude (deep scan)

## Project Overview
{1-3 sentence description of what this project is and its core objective}

## Progress Summary
| Area | Status | Notes |
|------|--------|-------|
| {feature/section} | ✅/🔧/⏳/👤/❓ | {brief note} |

## What's Complete
{Brief summary of what's been accomplished}

## What's Left

### Claude Can Handle
- {specific task}
- {specific task}

### Human Action Needed
- {specific task + why it needs a human}

### Needs Clarification
- {question for the user}

## Cleanup Recommendations
### Safe to Delete
- `path/to/file` — {reason}

### Should Update
- `path/to/file` — {what's outdated and what it should say}

### Code to Clean Up
- `path/to/file:L{line}` — {what's dead/unused}

## Recommendations for Next Session
{2-3 bullet priorities for what to work on next, in order}
```

### Phase 5: Present and Discuss

After generating STATUS.md:

1. **Summarize the key findings in chat** — Don't just say "see the file." Give the user a conversational recap: what the project is, how far along it is, what surprised you, and what you'd recommend tackling next.

2. **Ask before deleting anything.** List what you'd recommend removing, but let the user confirm. Some "unused" files are intentionally kept. Some "dead code" is a feature in progress.

3. **Ask before updating docs.** Offer to fix stale documentation, but confirm the intended direction first — maybe the docs describe the *target* state, not the current one.

4. **If CLAUDE.md is stale, offer to update it.** This is especially valuable pre-compact — an accurate CLAUDE.md means the next session starts with good context.

## Important Principles

- **Be honest about uncertainty.** If you can't tell whether something is unused or just hasn't been wired up yet, say so. Don't confidently recommend deleting files you're not sure about.

- **Respect the human's vision.** You're auditing, not redesigning. If the user has a particular structure or approach, work within it. Flag things that seem off, but don't unilaterally reorganize.

- **Prioritize the handoff.** The most valuable output of this skill isn't the cleanup — it's the STATUS.md and the in-chat recap. A future session (or a different person entirely) should be able to read STATUS.md and immediately understand where things stand.

- **Don't boil the ocean.** If the repo is large, focus on the areas that matter most. A thorough scan of the key directories beats a shallow scan of everything. Use your judgment about where to go deep.

- **The conversation history is context.** If you've been working with the user in this session, you already know a lot about what's been done. Use that knowledge. Don't ignore what happened in the conversation just because it wasn't committed yet.
