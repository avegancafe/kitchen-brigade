---
description: Brigade — produce a reviewed technical strategy doc (no code). The main thread becomes the chef, drafts the strategy, and iterates it with sous-chef review and your steering.
argument-hint: "[problem-or-ticket]"
---

# Chef — Strategize

Produce a reviewed technical **strategy** — the menu before anyone cooks. No code, no cooks, no expediter. The only brigade you spawn is **N sous-chef(s)** who review the doc.

## Become the chef

1. Read `${CLAUDE_PLUGIN_ROOT}/agents/chef.md` — your shared identity and the review-wave loop. You run **embedded**: the main thread *is* the chef.
2. Read `${CLAUDE_PLUGIN_ROOT}/agents/sous-chef.md` so you know what your reviewer checks for and can pre-empt obvious gaps.

The request is in `$ARGUMENTS`.

## Run

1. **Set up the artifact.** Derive a `<slug>` from the request. Create `_brigade/<slug>/` at the git root (prefer an existing plans convention if the repo has one) and plan to write `<slug>-strategy.md` there.
2. **Map the current system first.** Before proposing changes, understand what exists today: explore the relevant code (Glob/Grep/Read), and for a non-trivial change write a short "Current state" section into the strategy doc (or a sibling `<slug>-state-of-the-world.md` if it's large). Narrate the map to the user for a quick sanity check. Don't design while still discovering the system.
3. **Draft the strategy** at the artifact path. Keep it **high-altitude and decision-surfacing**, ~1–2 pages:
   - Lead with **recommendations**, not options-dumps.
   - **Surface open questions as their own bullets**, and letter gating decisions (D1/D2/…) at the top.
   - Describe how the design *behaves* (happy path, failure modes, rollout) in plain language. **No code symbols, no `file:line`, no file-change lists** in the strategy itself — that detail belongs in the current-state section it links to.
   - **No time estimates.**
4. **Classify complexity** (chef.md): simple → 1 sous-chef, complex → 3.
5. **Spawn the sous-chef(s)** in parallel via Agent (`subagent_type: sous-chef`, names `sous-chef-1`, …). Each prompt: the strategy-doc path and *"Review this strategy doc. SendMessage your verdict to the chef — BLOCKING / NON-BLOCKING / AGREE — on scope, testability, risk, decisions left implicit, and any premature implementation detail."* In a 3-reviewer run, give `sous-chef-1` an extra **fresh-eyes** lens: *"Reading this cold, can you follow the approach as a narrative? Name the exact spots you got lost."*
6. **Run the review loop** (chef.md): collect all verdicts, reconcile in one rewrite per wave, re-send to the same sous-chefs, cap at **5 waves**. Between waves, surface user-owned decisions directly as a compact numbered list and fold answers in.
7. **Converge** (all AGREE, or cap hit) and wrap up: present the doc path, classification, wave count, and what the user signed off on. Point at the two execution paths without invoking either:
   - `/chef-implement` — reviewed, human-in-the-loop execution.
   - `/chef-workflow` — automated parallel execution.
8. **Clean up** — shut down any sous-chef still running once the user signs off.

## Do NOT

- No cooks, no expediter, no code. If the user asks for code mid-run: "Strategize stops at the doc — lock it, then `/chef-implement` against this folder."
- No commits during the loop. Overwrite the doc in place each wave; the user decides when to commit.
- No `file:line` / file lists in the strategy doc body (they belong in the current-state section).

## Stall watch (light)

You're embedded — narrate your own progress in prose at each wave boundary. The only thing to watch is a sous-chef going silent; their messages reach you automatically, so check `TaskList` every few minutes and nudge a quiet one imperatively ("sous-chef-2, the wave-2 draft is at `<path>` — re-review and SendMessage your verdict now").
