# Kitchen Brigade — `/chef`

A lean, stack-agnostic multi-agent orchestration system for Claude Code, modeled on the **brigade de cuisine**. You summon the **chef**; the chef runs the brigade.

## Install

```bash
/plugin marketplace add avegancafe/avegancafe-marketplace
/plugin install kitchen-brigade@avegancafe-marketplace
```

Then restart Claude Code. (The marketplace and plugin repos are private — installation uses your GitHub SSH access.)

## The cast (4 roles)

| Role | Brigade title | Job |
|------|---------------|-----|
| **chef** | Chef de Cuisine | Plans the menu, coordinates the line, owns the pass. The main thread *becomes* the chef. |
| **sous-chef** | Sous-Chef | The second palate — reviews the **plan** (strategy / implementation plan / workflow script) before anyone cooks. |
| **cook** | Chef de Partie | The implementer. Works a **station** (a disjoint set of files) the chef assigns; TDD. Run several in parallel on non-overlapping lanes. |
| **expediter** | Aboyeur (the pass) | The single quality gate. Inspects every plate across four lenses: **code logic · tests/behavior · design fidelity · security.** Audits only; never cooks. |

> Consolidated from a 7-agent design → 4: the two stack-specific cooks merged into one generic cook, and the three post-build auditors (code QA, design QA, security) merged into the expediter's four-lens pass.

## The commands (service styles)

| Command | Style | Produces |
|---------|-------|----------|
| `/chef` | Router — picks a style and hands off | — |
| `/chef-strategize` | Reviewed technical strategy, no code | `_brigade/<slug>/<slug>-strategy.md` |
| `/chef-implement` | Build end-to-end: plan → TDD + checkpoints → final pass | `…-plan.md` + commits |
| `/chef-workflow` | Author + run a dynamic Workflow for large parallel work | `…-workflow.js` + edits |
| `/chef-diagnose` | Evidence-based root cause, read-only, no fix | `…-diagnosis.md` |

Artifacts land in **`_brigade/<slug>/`** at the git root (or an existing plans convention if the repo has one).

## Design principles

- **One coordinator locus.** The main thread always *is* the chef (embedded) — no spawned-conductor indirection. The chef spawns the cast directly.
- **Stack-agnostic.** No framework assumptions; the cook and expediter discover and use the project's own test/lint/typecheck tooling.
- **No hard external dependencies.** Codex, Playwright, and Figma are used *if present*, never required.
- **Lighter ceremony.** Simplified heartbeats, conservative git posture (cooks commit their lane; pushing is the user's call).

## Tuning

- Models: chef / sous-chef / expediter = **opus**; cook = **sonnet**. Adjust per agent-file frontmatter.
- Complexity fan-out: simple → 1 sous-chef, complex → 3. The chef classifies by sequential phase count.
- To add a specialist back (e.g. a dedicated security inspector), copy `agents/cook.md` or `agents/expediter.md` as a template and reference it from the relevant command.

## License

MIT
