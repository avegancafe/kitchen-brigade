---
description: Brigade — author and run a dynamic Workflow script for large-scale, parallelizable work. The main thread becomes the chef, writes the script, reviews its design with sous-chef(s), then runs it.
argument-hint: "[ticket-or-brigade-folder]"
---

# Chef — Workflow

Execute **decomposable, parallelizable** work by authoring a dynamic [Workflow](https://claude.com/blog/introducing-dynamic-workflows-in-claude-code/) script and running it — the parallel sibling of implement. Same cooks and expediter, but invoked as stateless one-shot workers inside a deterministic script.

## Become the chef

1. Read `${CLAUDE_PLUGIN_ROOT}/agents/chef.md` — shared identity and the review-wave loop. You run **embedded**: the main thread *is* the chef, so you can call the `Workflow` tool yourself (a subagent can't nest it — that's why this is embedded).
2. The request is in `$ARGUMENTS`.

## Suitability gate (first)

A dynamic workflow only pays off for **many independent units** (files, modules, call sites) that transform/audit/generate in parallel and verify independently — migrations, codemods, repo-wide audits, broad refactors. If the work is mostly **sequential / interdependent**, say so directly and recommend `/chef-implement` instead; don't force it. If it's *partly* suitable, recommend a hybrid (workflow for the independent groundwork, implement for the rest).

## Run

1. **Set up the folder.** Existing `_brigade/<slug>/` with a strategy → decompose it. Otherwise derive a `<slug>` and create `_brigade/<slug>/`. The script lives at `<slug>-workflow.js`.
2. **Decompose.** Explore the repo (Glob/Grep/Read) to discover the actual fan-out set — the files/call-sites each parallel agent will own. If the set isn't known up front, design a discovery stage that produces it before the transform stage.
3. **Author the script** at the artifact path. Rules:
   - Begin with a pure-literal `export const meta = { name, description, phases: [...] }`.
   - **Default to `pipeline()`**, not a `parallel()` barrier between stages — each item flows through all stages independently. Use a barrier only when a stage genuinely needs *all* prior results (dedup/merge, zero-count early-exit).
   - **Worker agents by `agentType`** — the brigade agents resolve by their bare names: `cook` for implementation, `expediter` for verification/audit. (`agent(prompt, { agentType: 'cook', schema, label, phase })`.)
   - **Tag every worker prompt `[mode:workflow]`** (prepend `const WF = '[mode:workflow]'`) so the agent drops its team-coordination behavior and runs one-shot.
   - **Force structured output** with a `schema` on every worker — never free prose. Transform → `{summary, files_changed, self_check_pass, notes}`; verify → `{target, real_problem, severity, evidence}`.
   - **Partition file-mutators across DISJOINT files — one cook per file.** All workers share one working tree; two agents writing the same file collide. If two units need the same file, merge them into one agent.
   - **Verify adversarially in a SEPARATE stage** — after a transform, run an `expediter` stage prompted to *refute* the change (default to "broken" when uncertain). Never hand a verifier the edit it's checking; mutation and verification are different stages (cook edits, expediter grades).
   - **Don't run the full suite inside a parallel transform stage** — each cook self-checks only its own file; run the authoritative suite once in a later single-owner stage.
   - **No `Date.now()` / `Math.random()`** (they throw); vary prompts by index. `log()` any coverage you cap so it doesn't read as "covered everything."
4. **Classify complexity** (chef.md) → `N` sous-chefs.
5. **Review the design.** Spawn `N` sous-chef(s) (`subagent_type: sous-chef`), prompt tagged `[mode:workflow]`: *"Review the workflow script at `<path>`. Are `parallel()` stages genuinely independent? Is anything sequential masquerading as parallel? Do verify stages + schemas catch failure? Are file-mutators partitioned across disjoint files? SendMessage your verdict to the chef — BLOCKING / NON-BLOCKING / AGREE."*
6. **Review loop** (chef.md), cap 5 waves, overwriting the `.js` in place. Surface user-owned calls directly (e.g. "OK to run a destructive codemod across 400 files?").
7. **Run it yourself.** On convergence, re-read the script to sanity-check, then `Workflow({ scriptPath: "<abs path>" })`. Watch `/workflows`; you'll get a notification when it finishes.
8. **Review the result.** Summarize what landed, what passed the verify stages, anything flagged. For a heavier final pass on the aggregate diff, recommend `/chef-implement`'s final gate.
9. **Clean up** — shut down any sous-chef still running.

## Do NOT

- No persistent cook/expediter team — they live *inside* the script; the Workflow tool spawns them. The only team you spawn is your sous-chef(s) for the design review.
- No TDD-checkpoint loop or per-task heartbeats — that's implement's machinery. Here the Workflow tool owns worker lifecycle.
