---
description: Kitchen-brigade orchestration — route a request to the right service style (strategize / implement / workflow / diagnose) and run the brigade.
argument-hint: "[request]"
---

# Chef — brigade router

The kitchen brigade runs work through one coordinator (the **chef**) and a lean cast: a **sous-chef** who reviews the plan, **cook(s)** who implement on disjoint stations, and an **expediter** who owns the pass (code + tests + design + security). It comes in four service styles. Your job here is to pick the right one and hand off — you don't run it yourself from this router.

| Style | When | Command |
|---|---|---|
| **Strategize** | A problem needs a reviewed technical strategy — not yet code. | `/chef-strategize` |
| **Implement** | A plan or clear ticket needs to be built, reviewed, and verified. Interdependent work, human in the loop. | `/chef-implement` |
| **Workflow** | Large-scale, parallelizable work (broad refactor, migration, codemod, audit). The chef authors a dynamic Workflow script and runs it. | `/chef-workflow` |
| **Diagnose** | A bug/regression needs evidence-based root cause — not a fix. Read-only investigators. | `/chef-diagnose` |

**Implement vs. workflow** is the fork worth getting right: both execute, but implement is reviewed/incremental/human-in-the-loop (judgment matters), while workflow is automated parallel fan-out with adversarial verification (decomposable work at scale). Default to implement; pick workflow only when the work is genuinely parallelizable.

## How to route

The request is in `$ARGUMENTS`.

1. **Read it for signal:**
   - *strategize, design, technical strategy, RFC, propose, how should we…* → **strategize**
   - *implement, build, ship, do this ticket, finish the plan in `_brigade/…`* → **implement**
   - *migrate, refactor across, codemod, sweep, audit the whole, hundreds of files, fan out* → **workflow**
   - *diagnose, investigate, debug, root cause, why is X happening, reproduce* → **diagnose**
2. **Clear signal → hand off.** Say one sentence ("Looks like implement — handing to `/chef-implement`.") and invoke that command via the Skill tool with the same request.
3. **Mixed or absent → ask once** with AskUserQuestion (the four styles, one-line each), then hand off. If the choice is implement-vs-workflow, frame it as reviewed/incremental vs. automated/parallel.

## Reading the room

- *"Start on project X"* — usually strategize, unless `_brigade/<slug>/` already has a strategy (then implement).
- *"X is broken in staging"* — diagnose first; suggest it even if they say "fix it," unless they assert the cause is known.
- *"Implement the strategy in `_brigade/2026-…-x/`"* — implement, no ambiguity.

If the user already typed `/chef-strategize` (or another variant) directly, they've chosen — this router isn't involved. It exists only for the bare `/chef` entry point and ambiguous prompts.
