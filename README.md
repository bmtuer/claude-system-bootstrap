# Claude System Bootstrap

**A reusable doc-driven workflow for working with Claude Code on long-running projects.**

If you've ever started a project with Claude Code and watched it slowly accumulate ad-hoc CLAUDE.md sections, scattered design docs, half-baked plan files, and rules-of-thumb that live only in your conversation history — this fixes that. Once.

The bootstrap is a Claude Code skill. You drop it into any project, run one command (`/bootstrap-claude-system`), and it interviews you (with a head start from scanning your codebase) before scaffolding a complete, opinionated working structure: a `CLAUDE.md` constitution, a roadmap → sprint → plans → systems pipeline, per-surface rule modules, and four ready-to-use slash commands (`/new-plan`, `/session-end`, `/preflight`, `/review-diff`). After it runs, you delete the bootstrap skill and the system is yours to evolve.

It was extracted from a real working project — a year-plus codebase with hundreds of plan files, dozens of system docs, and a mature working rhythm with Claude. The system is opinionated about **structure** (file layout, lifecycle, where things go) and intentionally blank about **content** (your specific rules, workstreams, and learned traps stay yours to fill in over time).

---

## Why this exists

Working with an AI on a project for more than a few weeks creates a context problem: the AI starts each session knowing nothing, and you start each session re-explaining things. The fix is to externalize the context into versioned files the AI reads on every session — not just code docs, but a constitution (CLAUDE.md), a forward-looking backlog, in-progress plan files, and living references for shipped systems.

Most projects either don't do this (and pay the re-explanation tax forever) or invent the structure ad-hoc per project (and never converge on something repeatable). The bootstrap is the structure, captured once, reusable everywhere.

It's also surface-portable in concept: the *meta-architecture* (the file layout and lifecycle) works equally well in Claude Desktop, Claude.ai web, or any other AI coding tool. The current v1 scaffolds Claude Code specifically (it depends on the `.claude/skills/` folder and slash-command invocation pattern), but the produced structure is yours regardless of how you invoke the AI.

---

## Who this is for

- You use Claude Code (the CLI / IDE extension) regularly.
- You're working on a project that will live longer than a few weeks.
- You want a repeatable doc structure across projects rather than reinventing one each time.
- You like the idea of plans-as-files, system docs that live next to code, and slash commands that respect a known structure.

If you're doing one-off scripting or short-lived prototypes, the structure is overkill. This is for projects with continuity.

---

## What you get

After running the bootstrap, your project gains:

```
your-project/
├── CLAUDE.md                     # AI context / project constitution
├── docs/
│   ├── roadmap.md                # Forward-looking backlog (workstream-tagged)
│   ├── sprint-backlog.md         # Current sprint whiteboard
│   ├── architecture.md           # Why-behind-the-rules
│   ├── plan-template.md          # Starter for new plan files
│   └── systems/
│       └── README.md             # Convention for living system references
├── plans/
│   ├── active/                   # In-progress feature work
│   └── shipped/                  # Archived plans
└── .claude/
    ├── rules/
    │   └── README.md             # Convention for per-surface rule modules
    └── skills/
        ├── new-plan/             # /new-plan slash command
        ├── session-end/          # /session-end slash command
        ├── preflight/            # /preflight slash command
        └── review-diff/          # /review-diff slash command
```

Plus, if you started from an existing codebase, the bootstrap will have pre-filled CLAUDE.md and roadmap.md with proposed workstreams, surfaces, and a project description based on what it found in your repo.

---

## Install + run

### Step 1 — Clone this repo somewhere

```bash
git clone https://github.com/YOUR_USERNAME/claude-system-bootstrap.git
```

(Or download as a zip, or `gh repo clone`. Doesn't matter how it lands on disk.)

### Step 2 — Copy the bootstrap skill into your target project

From inside your target project's root directory:

```bash
mkdir -p .claude/skills
cp -r /path/to/claude-system-bootstrap/.claude/skills/bootstrap-claude-system .claude/skills/
```

(On Windows PowerShell: `Copy-Item -Recurse C:\path\to\claude-system-bootstrap\.claude\skills\bootstrap-claude-system .claude\skills\`)

### Step 3 — Open Claude Code in your target project

Open the project in your IDE with Claude Code, or `cd` into it in your terminal and run `claude`.

### Step 4 — Run the bootstrap

In the Claude Code chat, type:

```
/bootstrap-claude-system
```

Claude will:

1. **Scan your repo** (~30 seconds) — reads `package.json` / `Cargo.toml` / similar, lists top-level directories, peeks at recent git log, samples a few source files to infer architecture.
2. **Interview you** (~5–10 questions) — proposes answers based on the scan; you confirm or redirect. Topics: project description, workstreams, surfaces / rule modules, hard rules (optional), known traps (optional), which slash commands to scaffold, retrofit handling.
3. **Scaffold the files** — writes everything listed in the "What you get" section above, with your answers baked in.
4. **Print next steps** — usually "open CLAUDE.md, edit anything that doesn't fit, run `/new-plan` to create your first plan."

### Step 5 — (Optional) Delete the bootstrap skill

The bootstrap skill has done its job. You can leave it in place (in case you want to re-run it later for some reason) or delete it:

```bash
rm -rf .claude/skills/bootstrap-claude-system
```

---

## What "greenfield" vs "retrofit" means

The bootstrap handles two cases:

**Greenfield** — your project is empty or near-empty. The interview leans more on Q&A (less to scan), and the produced files are mostly placeholders for you to fill in as you go.

**Retrofit** — your project already has code (and possibly some docs / a `.claude/` folder). The bootstrap scans more aggressively, proposes workstreams from your directory structure, and respects existing conventions where it can (e.g., it won't blindly overwrite an existing `docs/` folder — it'll ask first).

You don't choose the path; the skill detects which case applies and asks for confirmation.

---

## What's intentionally NOT included

A few things you might expect that the bootstrap deliberately leaves out:

- **`/prd`, `/simplify`, and other domain-specific slash commands.** The four bundled commands (`/new-plan`, `/session-end`, `/preflight`, `/review-diff`) are the load-bearing ones — they reference the doc structure the bootstrap created. Domain-shaped commands like `/prd` are powerful but project-specific; you'll grow your own as needs emerge.
- **Pre-filled "hard rules" or "known traps."** These come from real incidents in real projects. Yours will look different from anyone else's. The bootstrap leaves these sections empty with a placeholder and a `<!-- Add as you learn them -->` comment.
- **A specific tech stack assumption.** The bootstrap proposes workstreams based on what it finds, but the meta-architecture (roadmap → sprint → plans → systems) works for any kind of project, including non-code projects.

---

## After the bootstrap — using the system

Once scaffolded, the typical lifecycle for a piece of work is:

1. **Add the item to `docs/roadmap.md`** under the right workstream + priority.
2. **Pull it into `docs/sprint-backlog.md`** when you're ready to work it.
3. **Run `/new-plan`** to scaffold a `plans/active/YYYY-MM-DD-{slug}.md` file.
4. **Build it.** Use `/preflight` before non-trivial work to surface architectural concerns. Use `/review-diff` before commit to self-review.
5. **Run `/session-end`** at the end of each working session to update the plan's session log.
6. **When it ships:** update or create the relevant `docs/systems/*.md`, move the plan to `plans/shipped/`, remove the item from roadmap and sprint-backlog (done = gone, no `[x]` accumulation).

CLAUDE.md will explain this in more detail (the bootstrap writes it).

---

## Troubleshooting

**"`/bootstrap-claude-system` isn't recognized."**
Check that `.claude/skills/bootstrap-claude-system/SKILL.md` exists in your project root. Restart Claude Code after copying the skill in.

**"The scan proposed weird workstreams."**
That's fine — say so when it asks for confirmation, and propose your own. The scan is a starting point, not a final answer.

**"It tried to overwrite my existing CLAUDE.md / docs/."**
It shouldn't — it'll ask first. If it didn't, that's a bug; please tell Bryan.

**"I want to re-run the bootstrap with different answers."**
Easiest: revert the changes in git (`git reset --hard` if you haven't committed), then run again. The bootstrap doesn't have a "second-run" mode by design — clean slate is simpler.

**"I want to customize the slash commands the bootstrap installed."**
Edit them. They live at `.claude/skills/{command-name}/SKILL.md`. They're plain markdown — you own them once they're in your project.

---

## Adapting to other Claude surfaces

This v1 targets Claude Code specifically (it depends on the `.claude/skills/` folder convention and the slash-command invocation pattern).

The *meta-architecture it produces* (CLAUDE.md, docs/, plans/, systems/) is surface-agnostic and ports cleanly to:

- **Claude Desktop projects** — upload CLAUDE.md and the docs as project files; use the system manually without slash commands.
- **Claude.ai web** — same as Desktop.
- **Other AI coding tools** — the file structure works anywhere; you'd lose the slash commands but keep the lifecycle.

A future version may ship adapters for other surfaces. For now, this is Code-first.

---

## License

MIT. Use it, fork it, customize it, share it.
