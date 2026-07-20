---
name: dinner-rush
description: Use when something changed unexpectedly that you did not do — a file holding edits you don't remember making, an unfamiliar commit/branch/stash, a test that newly exists or changed result, a tracker issue that moved, or working state that differs from your last snapshot ("file modified by user or linter", "changed from under you") — to weigh whether a concurrent session caused it before treating it as a bug. Also use when the user says other Claude/agent sessions are running at the same time in the same repo or workspace (that confirms concurrency), or when working through tasks from a shared projects-plugin todo list (a `_projects/YYYY-MM-DD--<name>/todo.md`), so tasks get claimed by self-assigning the session id. Sets a cautious, change-tolerant posture; attributes peer changes to the other sessions instead of reverting or "fixing" them.
---

# Dinner Rush

## Overview

It's the dinner rush: the kitchen is slammed with simultaneous orders and you're not the only one working the pass. Another session may be editing the same working tree, moving git state (new commits, branches, stashes), or writing a shared issue tracker (beads) **while you work**. Adopt this posture for the rest of the session: **the workspace is shared and mutable under you.** Unexpected ≠ broken.

**Core principle: when something looks changed or off, suspect a peer session before you suspect a bug — and never destructively "fix" it.**

## Two ways you got here

- **You noticed a surprise (you pulled this skill in yourself).** Something changed that you didn't do. Treat "a concurrent session did this" as the **leading hypothesis** — not yet certain. Verify current state, do **not** revert, and rebase your mental model before proceeding. If it materially affects your work, confirm with the user rather than guessing.
- **The user invoked this skill.** Multiple sessions are **confirmed** running right now. Don't treat concurrency as hypothetical — hold the full posture below for the rest of the session, and expect the tree, git state, and tracker to move under you repeatedly.

## Attribution heuristic

Before reacting to anything surprising — a file carrying edits you didn't make, a commit or branch you don't recognize, a test that newly exists or whose result changed, a tracker issue whose status moved — assume **a concurrent session likely did it**, then:

- **Don't revert, overwrite, or "restore" it.** It is probably intentional peer work, not corruption.
- **Re-read current state** (`git status`, `git log --oneline -8`, re-Read the file) instead of trusting your earlier snapshot.
- **If it blocks you or genuinely looks wrong, ask the user** — they are coordinating the sessions — rather than acting on it unilaterally.

## Operate carefully

- Prefer **narrow, lane-scoped, reversible** actions. Stage only the files you yourself changed.
- **Re-check `git status` / `git log` immediately before** any write-heavy or irreversible git operation — state may have moved since you last looked.
- **Re-Read a file right before editing** if any time has passed; edit against the current bytes, not a stale memory of them.
- Assume your own uncommitted work could collide with a peer's — commit your lane promptly so it's durable and attributable.

## Claiming tasks in shared project todos

When you pull work from a projects-plugin todo list (`_projects/YYYY-MM-DD--<name>/todo.md`), peer sessions may be pulling from the same list. Claim before you cook:

- **Self-assign on claim.** Before starting a task, add this session's assignee link to the task line: `[assignee:<readable-id>]`. Get the readable id (adjective-noun pair like `brave-otter`) via the `session-ids:session-id` skill (`session-ids` is a declared dependency of this plugin, so it installs alongside it) — its SessionStart hook injected `This session's readable id is "…"` at startup; if that context is gone, use the skill's state-file fallback. Note the `@high/@medium/@low` @-mentions on the line are *priorities* — leave them alone.
- **Respect existing claims.** A task already carrying another session's `[assignee:…]` link is probably in flight on a peer session — skip it unless the user reassigns it to you.
- **Keep the line intact.** On completion flip `- [ ]` → `- [x]` and keep the priority and every assignee link, yours and peers'. Never strip a peer's link.
- **Degrade gracefully.** If the `session-ids` skills are somehow unavailable (dependency disabled or removed — no readable id exists), skip the self-assignment rather than inventing an id.
- **Resolve peers when needed.** To map a peer's `[assignee:<id>]` link back to a session, use `session-ids:list-sessions`; `session-ids:session-info` gives full details on the current session.

## Risky operations — avoid or narrow

| Don't | Do instead |
|---|---|
| `git add -A` / `git commit -am` | `git add <only your files>` |
| `git reset --hard`, `git checkout -- .`, `git clean` | Leave others' changes alone; stash only your own if needed |
| force-push, delete/rename branches, rebase a shared branch | Coordinate with the user first |
| Mass reformat / lint-fix across the tree | Limit to files you are actively editing |
| Delete "stray" or unrecognized files | Assume a peer created them; ask before removing |
| Bulk tracker writes (`bd` mass close/relabel/delete) | Touch only the issues you own this session |

## Red flags — stop and reconsider

- "This file changed from what I expected, I'll revert it" → **no — a peer changed it. Rebase your model.**
- "This commit/branch/stash looks like junk, I'll reset it away" → **no — verify with the user first.**
- "Files or tests I didn't create are here, something's broken" → **expected in shared work; don't treat it as breakage.**
- "A peer's change is half-finished or breaks the build, I'll revert it / finish it for them" → **no — it's likely mid-flight. Unblock additively (e.g. install a missing dep) or ask; don't revert, complete, or clobber their work.**
- About to run a tree-wide, destructive, or history-rewriting command → **re-check state and narrow the scope first.**

## First moves

Whichever way you got here: re-establish current state (`git status`, recent `git log`, and the relevant tracker view) before acting, and rebase your mental model onto what's actually on disk now. If the user invoked this, also fold in any specifics they gave (which repos, how many sessions, who owns what) and carry the posture for the remainder of the session.
