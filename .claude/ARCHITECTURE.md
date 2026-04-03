# Architecture

## Entry Flow

1. **User invokes `/dev-workflow`** → Claude Code loads `plugins/dev-workflow/skills/dev-workflow/SKILL.md` → mode detected from args/context → mode reference file loaded → mode executed
2. **User invokes `/ship`** → loads `plugins/dev-workflow/skills/ship/skill.md` → 6-phase pre-flight → commit → push → optional PR creation
3. **User invokes `/audit`** → loads `plugins/dev-workflow/skills/audit/skill.md` → dispatches role-based subagents → findings report → approval gate → fixes
4. **User invokes `/qa`** → loads `plugins/dev-workflow/skills/qa/skill.md` → browser-based viewport testing

## Directory Layout

```
dev-workflow-plugin/
├── CLAUDE.md                              # Project instructions for Claude
├── README.md                              # Public-facing docs
├── .gitignore
├── .claude/                               # Living documentation
│   ├── ARCHITECTURE.md                    # This file
│   ├── TASKS.md                           # Phase tracking
│   └── CHANGELOG.md                       # Doc update log
├── .claude-plugin/
│   └── marketplace.json                   # Top-level marketplace manifest (v3.0.0)
├── plugins/
│   └── dev-workflow/
│       ├── .claude-plugin/
│       │   └── plugin.json                # Plugin manifest (v3.0.0)
│       └── skills/
│           ├── dev-workflow/
│           │   ├── SKILL.md               # Main workflow engine (scaffold/execute/handoff/resume/audit/upgrade)
│           │   └── references/
│           │       ├── mode-scaffold.md           # Scaffold mode (framework detection, git analysis, hooks, PRD)
│           │       ├── mode-execute.md            # Execute mode (PRP-aware, agent delegation)
│           │       ├── mode-audit.md              # Audit mode instructions
│           │       ├── mode-upgrade.md            # Upgrade mode instructions
│           │       ├── claude-md-template.md      # Generic CLAUDE.md template
│           │       ├── claude-md-nextjs.md        # Next.js CLAUDE.md template
│           │       ├── claude-md-go.md            # Go CLAUDE.md template
│           │       ├── claude-md-rust.md          # Rust CLAUDE.md template
│           │       ├── claude-md-fastapi.md       # FastAPI CLAUDE.md template
│           │       ├── claude-docs-scaffold.md    # .claude/ file templates
│           │       ├── doc-evolution-rules.md     # Rules for updating .claude/ docs
│           │       ├── hooks-templates.md         # Hook templates (profile-gated)
│           │       ├── rules-typescript.md        # TypeScript conventions/rules
│           │       ├── rules-go.md                # Go conventions/rules
│           │       ├── rules-rust.md              # Rust conventions/rules
│           │       ├── rules-python.md            # Python conventions/rules
│           │       ├── agent-code-reviewer.md     # Code quality subagent
│           │       ├── agent-security-reviewer.md # OWASP/secrets subagent
│           │       ├── agent-build-resolver.md    # Build failure subagent
│           │       ├── agent-performance-optimizer.md # Performance subagent
│           │       └── agent-doc-updater.md       # Doc sync subagent
│           ├── ship/
│           │   └── skill.md               # 6-phase verification → commit → push → PR
│           ├── audit/
│           │   └── skill.md               # Structured project audit with parallel agents
│           └── qa/
│               └── skill.md               # Browser-based visual QA
└── docs/
    └── superpowers/specs/                 # Design specs (gitignored, used for planning)
```

## Key Relationships

- **marketplace.json** (root) registers the plugin for Claude Code's plugin system
- **plugin.json** (per-plugin) declares version, description, and metadata
- **SKILL.md / skill.md** files are the actual skill content loaded by Claude Code when a slash command is invoked
- **references/** files are loaded on-demand by skills — never speculatively by Claude Code itself
- The `docs/` directory is gitignored — used for internal planning only
- **Framework templates** (`claude-md-*.md`) are selected by scaffold mode based on project detection
- **Language rules** (`rules-*.md`) are merged into generated CONVENTIONS.md during scaffold
- **Agent definitions** (`agent-*.md`) are dispatched by `/audit` and execute mode Phase B/D
- **Hook templates** (`hooks-templates.md`) are generated into user projects, gated by profile level
