# Claude System Bootstrap

A reusable doc-driven workflow for working with Claude Code on long-running projects.

If you've ever started a project with Claude Code and watched it slowly accumulate ad-hoc CLAUDE.md sections, scattered design docs, half-baked plan files, and rules-of-thumb that live only in your conversation history: this tries to fix that.

The bootstrap is a Claude Code skill. Drop it into any project, run `/bootstrap-claude-system`, and it interviews you (with a head start from scanning your codebase) before scaffolding a complete working structure: a `CLAUDE.md` constitution, a roadmap → sprint → plans → systems pipeline, per-surface rule modules, and four ready-to-use slash commands (`/new-plan`, `/session-end`, `/preflight`, `/review-diff`). After it runs, delete the bootstrap and the system is yours to evolve.

Extracted from a real codebase. Opinionated about **structure** (file layout, lifecycle), intentionally blank about **content** (your engineering principles, workstreams, and learned traps stay yours).

## Why this exists

Working with an AI on a project for more than a few weeks creates a context problem: the AI starts each session knowing nothing, and you start each session re-explaining things. The fix is to externalize context into versioned files the AI reads every session — a constitution, a forward-looking backlog, in-progress plan files, and living references for shipped systems.

Most projects either don't do this (and pay the re-explanation tax forever) or invent the structure ad-hoc per project (and never converge on something repeatable). Alternatively, you can pay a SaaS company. This bootstrap is the structure, free, and reusable everywhere.

The meta-architecture is surface-portable — it works equally well in Claude Desktop or any AI coding tool. v1 scaffolds Claude Code specifically (depends on `.claude/skills/` and slash commands), but the produced structure is yours regardless of how you invoke the AI.

## Who this is for

- You use Claude Code regularly.
- You're working on a project that will live longer than a few weeks.
- You want a repeatable doc structure across projects rather than reinventing one each time.

For one-off scripts or short-lived prototypes, the structure is overkill.

## What you get

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

If you start from an existing codebase, the bootstrap pre-fills CLAUDE.md and roadmap.md with proposed workstreams, surfaces, and a project description based on what it found.

## Install + run

**1. Clone this repo:**
```bash
git clone https://github.com/bmtuer/claude-system-bootstrap.git
```

**2. Copy the bootstrap skill into your target project:**
```bash
# From your target project's root:
mkdir -p .claude/skills
cp -r /path/to/claude-system-bootstrap/.claude/skills/bootstrap-claude-system .claude/skills/
```
Windows PowerShell: `Copy-Item -Recurse C:\path\to\claude-system-bootstrap\.claude\skills\bootstrap-claude-system .claude\skills\`

**3. Open Claude Code in your target project**, then run:
```
/bootstrap-claude-system
```

Claude will scan your repo, ask 5–10 questions (proposing answers from the scan), scaffold the files, and print next steps.

**4. (Optional) Delete the bootstrap skill** once it's done:
```bash
rm -rf .claude/skills/bootstrap-claude-system
```
