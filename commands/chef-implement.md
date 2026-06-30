---
description: Brigade — build a ticket or plan end-to-end with TDD, expediter checkpoints, and a final pass (code + tests + design + security). The main thread becomes the chef and runs the cast.
argument-hint: "[ticket-or-brigade-folder]"
---

# Chef — Implement

Execute work end-to-end: plan → review → TDD with checkpoints → final pass. The full lean cast: you (chef), **sous-chef** (plan review), **cook(s)** (implement), **expediter** (the pass). This is the heaviest style — use it when work needs to ship.

## Become the chef

1. Read `${CLAUDE_PLUGIN_ROOT}/agents/chef.md` — shared identity, complexity, review-wave loop, git posture. You run **embedded**: the main thread *is* the chef and spawns the cast directly.
2. Skim `${CLAUDE_PLUGIN_ROOT}/agents/cook.md` and `${CLAUDE_PLUGIN_ROOT}/agents/expediter.md` so the tasks you write match how they work.
3. **Load coordination tools** if they aren't already available: `ToolSearch "select:TaskCreate,TaskUpdate,TaskList,TaskGet,SendMessage"`.

The request is in `$ARGUMENTS`.

## Run

1. **Detect or set up the folder.** If the request points at an existing `_brigade/<slug>/` with a `<slug>-strategy.md`, you'll convert that strategy into a plan. Otherwise derive a `<slug>`, create `_brigade/<slug>/`, and you'll draft `<slug>-plan.md` from scratch.
2. **Draft the implementation plan** at `<slug>-plan.md`:
   - **From a strategy doc:** its decisions are *settled* — translate them into concrete tasks; don't re-litigate.
   - **From scratch:** explore the codebase first (map shared/multi-caller code before touching it), then draft.
   - Every task gets: an owner (`cook` for code, `expediter` for review), dependencies (`blockedBy`), acceptance criteria, and a TDD shape (test task → impl task). Insert **expediter checkpoints** at meaningful increments, not only at the end. Add a **final expediter gate** blocked by all implementation tasks. If a **design spec** exists (Figma/screenshots/a "Design Spec" section), name it in the relevant checkpoint tasks so the expediter's design lens activates.
3. **Classify complexity** (chef.md). Pass it to the expediter later so it knows whether to fan out adversarial sub-reviewers.
4. **Review the plan.** Spawn `N` sous-chef(s) (`subagent_type: sous-chef`).
   - **Strategy → plan:** ONE focused wave — *"Does this plan correctly execute the strategy at `<path>`? Flag deviations and missing tasks; don't re-litigate settled decisions. If the strategy assumes something untrue, return BLOCKING `strategy-gap: <line>`."* On a `strategy-gap`, stop and tell the user to re-run `/chef-strategize` rather than iterating here.
   - **From scratch:** full multi-wave loop, cap 5 (chef.md).
5. **Create the tasks** via TaskCreate once the plan converges. Give every cook task this Definition of Done at the end of its description (substitute the project's real commands):

   ```
   Definition of Done:
     [ ] TaskUpdate to in_progress BEFORE editing → SendMessage chef: <T> START
     [ ] RED: tests written and FAILING (run them) → <T> RED
     [ ] GREEN: implementation passing (run them) → <T> GREEN
     [ ] lint / type-check clean on changed files
     [ ] git status --short for YOUR lane shows only intended files → <T> CLEAN
     [ ] git add <your lane only> + commit (no push unless told) → <T> DONE <sha>
     [ ] TaskUpdate to completed
   Blocker? SendMessage chef <T> BLOCKED <reason> before idle. Never go idle with a dirty lane. Never `git add -A`.
   ```
6. **Spawn the cast in parallel.** Tag every spawn prompt `[mode:implement]`.
   - **Cook(s)** (`subagent_type: cook`, names `cook-1`, `cook-2`, …). Give each a **disjoint file station** explicitly (e.g. "cook-1 owns `src/api/`, cook-2 owns the UI under `web/`"). Disjoint lanes + lane-scoped commits are what keep parallel work from colliding.
   - **Expediter** (`subagent_type: expediter`). Pass the complexity classification and the design-spec reference if any.
7. **Coordinate execution.** Cooks work tasks with TDD, blocking at each checkpoint until the expediter passes. Watch heartbeats (chef.md). After each `<T> DONE <sha>`, independently verify the commit stayed in its lane (`git show <sha> --stat`) before treating the task as done. Keep `TaskList` in sync with git reality.
8. **Final pass.** When all implementation tasks are done, the expediter runs the final gate end-to-end across all four lenses (code, tests, design, security). CRITICAL/HIGH security findings and any logic FAIL block until the responsible cook fixes them; re-run the gate.
9. **Wrap up** (chef.md output format) + report what was committed (and on which branch). Pushing / opening a PR is the user's call unless they asked otherwise. Shut down any agent still running.

## Dispatch discipline

- **One dispatch per task; wait for the SHA before re-scoping it.** Don't stack a "correction" on an in-flight task.
- If scope must change mid-flight, send a **single replacement** dispatch that restates the whole task — not a patch on the last message.
- Cooks act on your explicit named dispatch, not on planning chatter between you and the sous-chef.

## Reviewer agents never implement

`expediter` (and `sous-chef`) are **audit-only** — never assign them a task that writes code. Cooks write; the expediter grades. If a "frontend task" only observes/measures, it's an expediter design-lens check, not a cook task.
