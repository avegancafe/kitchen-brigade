---
name: yes-chef
description: Use when the user greenlights execution of an already-agreed plan — typing /yes-chef or saying "yes chef", "start cooking", "fire it", "go build it", "ship it", "execute the plan", "make it happen" — after a plan, spec, or design already exists from brainstorming, a strategy session, or a _brigade plan. Signals approval to begin building, not to plan.
---

# Yes, Chef — fire the line

`/yes-chef` is the **greenlight**. The user is confirming that an already-agreed plan is approved and it's time to build. The plan already exists — your job is to **start executing it**, not to re-plan, re-confirm, or re-open settled decisions.

## What to do (in order)

1. **Find the agreed plan:**
   - A `_brigade/<slug>/<slug>-plan.md` (or `-strategy.md`) at the git root, or
   - a design/spec from a recent brainstorming or strategize session, or
   - the plan just agreed in the conversation above.
2. **No plan found → stop and route to planning.** Do NOT fabricate one. Say there's nothing approved to execute and suggest brainstorming or `/chef-strategize` first. `/yes-chef` executes a plan; it never invents one.
3. **Plan found → execute it now.** `/yes-chef` *is* the confirmation — do not ask "are you sure?" or re-summarize the plan for re-approval. Begin building:
   - Plan lives in `_brigade/<slug>/` → hand off to **`/chef-implement`** (the brigade builds it with TDD + review gates).
   - Otherwise → execute the agreed plan directly, test-first (use superpowers:test-driven-development), tracking work in beads.

## Guardrails

- **Don't re-litigate.** Settled decisions stay settled; surface only a genuinely new blocker.
- **Don't re-plan.** If the plan needs a small change, make it and proceed — don't restart planning.
- **Don't cook without a plan.** No approved plan = route to planning, not improvisation.

`/yes-chef` = "the plan's good — go."
