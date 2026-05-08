# Design notes — Claude System Bootstrap

> Internal: why the bootstrap is shaped the way it is. Not for end users — for future maintainers (you, me, anyone iterating on this).

## Origin

The bootstrap extracts a doc-driven workflow that emerged organically from a single real project (a game codebase). The system worked there because it was shaped by real incidents — every rule, trap, and convention earned its place by being load-bearing for some past failure.

The challenge in extracting it: the *structure* (CLAUDE.md sections, roadmap → sprint → plans → systems pipeline, rule-module pattern) is portable. The *content* (specific rules, specific traps, specific workstream taxonomies) is not.

## Design principles

### 1. Ship the scaffold, not the scar tissue

Other people's projects will have their own incidents and their own rules. Pre-filling Hard rules with "never commit secrets" or pre-filling Known traps with generic warnings would be cargo-culting — the user might keep them out of deference rather than because they're real.

So: Hard rules and Known traps default to **empty placeholders** with `<!-- Add as you learn them -->` comments. The self-improvement directive in CLAUDE.md prompts Claude to suggest additions as patterns emerge. The list grows with the project.

### 2. Propose-then-confirm beats cold Q&A

Cold prompts ("What are your workstreams?") get vague answers. Concrete proposals from a codebase scan ("I see `src/`, `server/`, `data/` — proposing Frontend, Backend, Data — sound right?") get fast confirmations or fast redirects.

The bootstrap scans first, proposes second, asks third. The interview is reactive, not generative.

### 3. Greenfield and retrofit are first-class

Half the use cases will be retrofit (existing project that wants to adopt the system). The bootstrap detects this case via the scan and adjusts:
- Doesn't overwrite existing files without confirmation.
- Asks how to integrate with existing `docs/` or `.claude/` if found.
- Falls back to pure Q&A only when the scan finds nothing.

### 4. The bundled slash commands are the four that need the doc structure

`/new-plan` writes to `plans/active/` using `docs/plan-template.md`. `/session-end` updates plan files. `/preflight` reads `CLAUDE.md` + `.claude/rules/` + `docs/systems/`. `/review-diff` reads the same plus the diff.

These four are *load-bearing on the doc structure*. They're also general-purpose — every project benefits from "scaffold a plan," "log a session," "preflight," "review."

Domain-specific commands like `/prd` (which interviews for a product requirements doc) are powerful but project-shaped. The bootstrap deliberately leaves those out — users grow them as needs emerge.

### 5. The bootstrap skill is disposable

After running, the bootstrap has done its job. The README tells users they can delete `.claude/skills/bootstrap-claude-system/` afterward. Re-running the bootstrap on a populated project is not a supported flow — clean slate is simpler than reconciliation.

## Open questions / future work

- **Multi-language scan accuracy.** The scan currently understands JS/TS, Rust, Python, Go, Ruby, PHP, Java. Languages outside that list fall back to directory-shape inference.
- **Adapter for Claude Desktop.** v1 is Code-only. Desktop port would replace `.claude/skills/` with Project instructions + uploaded files. The CLAUDE.md and docs/ structure ports as-is.
- **`/prd` and `/simplify` ports.** Worth shipping as optional add-ons in a future version? They were load-bearing in the source project. Decision deferred until v1 has real usage.
- **Re-run support.** Currently not a goal. If someone wants to re-run the bootstrap with different answers, they revert in git and re-run. Cleaner than a "merge with existing" mode.

## Source-project parallels

For maintainers who want to compare:

| Bootstrap concept | Source project equivalent |
|---|---|
| `CLAUDE.md` template | The source project's CLAUDE.md (heavily project-specific in the original) |
| `docs/roadmap.md` template | `docs/roadmap.md` |
| `docs/sprint-backlog.md` template | `docs/sprint-backlog.md` |
| `docs/plan-template.md` | `docs/plan-template.md` |
| `docs/systems/` convention | `docs/systems/` |
| `.claude/rules/` convention | `.claude/rules/` |
| `/new-plan` | `/new-plan` (source project version is more elaborate) |
| `/session-end` | `/session-end` |
| `/preflight` | `/preflight` |
| `/review-diff` | `/review-diff` |

The bootstrap's versions are deliberately stripped of domain content. They keep the structural moves and drop the project-specific guidance.
