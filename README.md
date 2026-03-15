# forge-skill

A Claude Code skill that creates other Claude Code skills. The inception — a skill that forges skills.

## What is this?

`forge-skill` is a **Claude Code slash command** — a markdown instruction file that Claude Code interprets when you type `/forge-skill`. It generates properly formatted skill files from 6 built-in template skeletons, validates naming, checks for duplicates, and registers the new skill automatically.

No runtime. No dependencies. No server. Pure prompt engineering.

## Install

Copy `forge-skill.md` to your Claude Code commands directory:

```bash
# Global (available in all projects)
cp forge-skill.md ~/.claude/commands/forge-skill.md

# Project-level (one repo only)
mkdir -p .claude/commands
cp forge-skill.md .claude/commands/forge-skill.md
```

That's it. `/forge-skill` is now available in Claude Code.

## Usage

```
/forge-skill <name> <scope> "<purpose>" [--template <type>]
```

### Arguments

| Argument | Required | Description |
|----------|----------|-------------|
| `name` | Yes | kebab-case skill name (e.g. `check-icons`) |
| `scope` | Yes | `global` (~/.claude/commands/) or `project` (.claude/commands/) |
| `purpose` | Yes | Quoted description of what the skill does |
| `--template` | No | One of: `audit`, `build`, `research`, `fix`, `swarm-dispatch`, `utility` |

### Examples

```bash
# Create an audit skill globally
/forge-skill check-deps global "Scan extensions for outdated dependencies" --template audit

# Create a utility skill for the current project
/forge-skill grep-todos project "Find and categorize TODO comments"

# Create a build skill
/forge-skill gen-icons global "Generate icon set from SVG source" --template build

# Create a quick-fix skill
/forge-skill patch-manifest project "Fix common manifest.json issues" --template fix
```

## Templates

If `--template` is omitted, the forge infers the right one from keywords in your purpose:

| Template | Inferred from | Structure | Use case |
|----------|--------------|-----------|----------|
| `audit` | "check", "scan", "verify", "validate" | Memory load, domain checks, summary table, KB update | Checking/scanning/verifying |
| `build` | "create", "build", "generate", "scaffold" | Load context, plan, write, verify, postmortem | Creating/generating things |
| `research` | "investigate", "analyze", "explore" | Dry run, investigate, synthesize, next steps | Investigating/analyzing |
| `fix` | "fix", "repair", "patch", "hotfix" | Locate, read, fix, verify | Quick targeted repairs |
| `swarm-dispatch` | "swarm", "deploy agents", "orchestrate" | Agent selection, 4-wave execution | Multi-agent orchestration |
| `utility` | (default fallback) | YAML frontmatter, steps, examples | Simple tools |

## What happens when you run it

1. **Validate** — checks kebab-case naming, scans for duplicates in both global and project directories, warns if skill count is getting high
2. **Select template** — matches `--template` flag or infers from purpose keywords
3. **Generate** — fills template skeleton with your name, purpose, date, and `$ARGUMENTS` placeholder
4. **Write** — creates the .md file and reads it back to verify
5. **Register** — appends entry to `tool-changelog.md` (if you use one) for tracking

## Safety

- **Duplicate detection** — checks both global and project directories before writing
- **Skill count warning** — alerts at >25 global or >10 project skills
- **Quality gate** — verifies valid markdown, `$ARGUMENTS` presence, no hardcoded paths
- **Never overwrites** — refuses to create if a skill with the same name exists

## Agent swarm integration

If you run agent swarms, agents can log `FORGE CANDIDATE: <name> -- <purpose>` to session notes during execution. These candidates get surfaced during post-build review, where you can choose to forge them into real skills. This prevents context pollution during active work while still capturing reusable patterns.

## What it doesn't do

- Modify existing skills (edit them directly or use a review workflow)
- Create directory-based skills (SKILL.md + reference files)
- Auto-test generated skills
- Compose skills that call other skills

## Requirements

- [Claude Code](https://docs.anthropic.com/en/docs/claude-code) CLI

## License

MIT

---

*Built by [Joona](https://github.com/Joona-t) — part of the LoveSpark open-source ecosystem*
