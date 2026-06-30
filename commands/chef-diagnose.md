---
description: Brigade — establish evidence-based root cause for a bug or regression (no fix). The main thread becomes the chef and spawns read-only cooks + an expediter to gather evidence and reproduce.
argument-hint: "[bug-or-question]"
---

# Chef — Diagnose

Establish root cause with **empirical evidence**. The brigade investigates read-only; **nothing gets fixed in this run**. When the user accepts the diagnosis, they can run `/chef-implement` against the same folder to fix it.

## Become the chef

1. Read `${CLAUDE_PLUGIN_ROOT}/agents/chef.md` — shared identity. You run **embedded**: the main thread *is* the chef, coordinating investigators *and* acting as the user's seat.
2. **Load coordination tools** if needed: `ToolSearch "select:TaskList,SendMessage"`.

The request is in `$ARGUMENTS`.

## Read-only is the contract

There's no hook enforcing it — read-only rests on the agents' diagnose-mode rules **plus your vigilance**:

- Every investigator spawn prompt **begins with `[mode:diagnose]`** — that's the read-only signal the cook/expediter read.
- Investigators do not edit, scaffold, migrate, or commit. If one finds the fix, it reports the root cause and does **not** apply it.
- **You are the safety net.** Between synthesis steps, run `git status --short` in the working tree — it should be empty. A non-empty status means an investigator started editing: nudge them to `git restore` and report findings via SendMessage instead.

## Run

1. **Set up the folder.** Derive a `<slug>` from the bug; create `_brigade/<slug>/` and `<slug>-diagnosis.md`.
2. **Classify the domain** (systems / interface / both) and **seed the diagnosis doc**: problem statement (user's words), reproduction steps, and 2–4 initial `H<id>` hypotheses you can articulate before evidence comes in.
3. **Spawn the investigators**, each prompt tagged `[mode:diagnose]` and told to SendMessage findings to the chef:
   - **Cook(s)** (`subagent_type: cook`) — hunt the relevant code/logs read-only (Read, Grep, Glob, read-only Bash; for UI bugs, read-only browser inspection — console, network, screenshots). Cite `file:line` and log/console excerpts. Use one cook per domain (a systems cook and/or a UI cook).
   - **Expediter** (`subagent_type: expediter`) — the **reproduction verifier**: reproduce the issue empirically (run the failing test, hit the endpoint, walk the UI), then verify each hypothesis against observed behavior. Reports `REPRO-PASS/FAIL` and `VERIFY <H-id> PASS|FAIL|INCONCLUSIVE` with evidence.
4. **Synthesize as evidence arrives.** Append to the diagnosis doc's evidence log (each entry: who, what, `file:line` / log excerpt / screenshot path). Move hypotheses between **Eliminated** (with why) and **Leading** (with supporting evidence). Run your `git status --short` read-only check between synthesis steps.
5. **User checkpoint — direct.** When a leading hypothesis is backed by reproducible evidence, present it in the main thread: *"Diagnosis converged. Leading root cause: `<one line>`. Evidence: `<bullets>`. Doc: `<path>`. (a) accept and run `/chef-implement` to fix, (b) push for more evidence, or (c) stop here?"*
6. **On more evidence:** translate the ask into a specific directive (e.g. "five production-log examples from the last 24h") and re-engage the investigators.
7. **On accept/stop:** finalize the doc — TL;DR at top, root-cause paragraph, and a **recommended fix direction** (a hint for implement, NOT a fix). Shut down the investigators. Do **not** auto-invoke `/chef-implement` — that's the user's call.

## Diagnosis doc shape

Problem statement · Reproduction steps · Evidence log (chronological, each cited) · Hypotheses (eliminated + leading) · Root cause (once converged) · Recommended fix direction · Out-of-scope discoveries.

You own this doc — investigators SendMessage you; you incorporate. Don't let unrelated discoveries expand the run; capture them under Out-of-scope.

## Do NOT

- No sous-chef (no plan to review), no final QA gate, no implementation tasks. Tasks are evidence-gathering only.
- Do not apply a fix, even if you're certain. Do not commit the doc during the loop.
