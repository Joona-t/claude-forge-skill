# claude-forge-skill

**The meta-skill that lets Claude build its own tools.**

`/forge-skill` turns Claude into a self-expanding toolkit. One command creates production-ready slash commands with proper validation, 6 smart templates, and agent swarm integration.

No runtime. No dependencies. No server. One markdown file. Pure prompt engineering.

## Why this exists

Agent swarms kept discovering repeatable workflows during execution — patterns that got written inline into reports and then forgotten. There was no way for an agent to codify a reusable pattern as a skill without stopping everything and hand-authoring a `.md` file. The forge fixes that. Any agent or user can create the skill they need, on the fly.

## Features

- **6 smart templates** — audit, build, research, fix, swarm-dispatch, utility
- **Auto-inference** — skip `--template` and the forge picks one from your purpose description
- **Duplicate protection** — checks both global and project directories before writing
- **Scope control** — global (`~/.claude/commands/`) or project (`.claude/commands/`)
- **Name validation** — enforces kebab-case, warns on skill count bloat
- **Auto-registration** — logs every forged skill to `tool-changelog.md`
- **Swarm-ready** — agents log `FORGE CANDIDATE` during runs, you approve in postmortem

## Install

```bash
# One file. That's it.
cp forge-skill.md ~/.claude/commands/forge-skill.md
```

`/forge-skill` is now available in Claude Code.

## Usage

```
/forge-skill <name> <scope> "<purpose>" [--template <type>]
```

### Examples

```bash
# Audit skill — scans for outdated deps
/forge-skill check-deps global "Scan extensions for outdated dependencies"

# Utility skill — project-scoped
/forge-skill grep-todos project "Find and categorize TODO comments"

# Build skill — generates assets
/forge-skill gen-icons global "Generate icon set from SVG source" --template build

# Fix skill — targeted repair
/forge-skill patch-manifest project "Fix common manifest.json issues" --template fix

# Swarm skill — multi-agent orchestration
/forge-skill deep-review global "Deploy agents to review an extension" --template swarm-dispatch
```

## Templates

| Template | Auto-inferred from | What it generates |
|----------|-------------------|-------------------|
| `audit` | "check", "scan", "verify", "validate" | Memory load > domain checks > summary table > KB update |
| `build` | "create", "build", "generate", "scaffold" | Load context > plan > write > verify > postmortem |
| `research` | "investigate", "analyze", "explore" | Dry run > investigate > synthesize > next steps |
| `fix` | "fix", "repair", "patch", "hotfix" | Locate > read > fix > verify |
| `swarm-dispatch` | "swarm", "deploy agents", "orchestrate" | Agent selection > 4-wave execution > reports |
| `utility` | *(default)* | Frontmatter > steps > examples > when-to-use |

## How it works

1. **Validate** — kebab-case check, duplicate scan, skill count warning
2. **Select template** — match `--template` flag or infer from keywords
3. **Generate** — fill skeleton with name, purpose, date, `$ARGUMENTS` placeholder
4. **Write** — create the `.md` file, read back to verify
5. **Register** — append to `tool-changelog.md` for tracking

## Swarm integration

Agents log `FORGE CANDIDATE: <name> -- <purpose>` to session memory when they spot a repeatable workflow. Candidates surface during `/postmortem`, where you approve or skip. Forging never happens mid-execution — only post-build. This prevents context pollution while capturing every reusable pattern.

## Requirements

- [Claude Code](https://docs.anthropic.com/en/docs/claude-code) CLI

## License

MIT

---

*Built by [Joona](https://github.com/Joona-t)*
