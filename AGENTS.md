# AGENTS.md

## Project Overview

- Repository for coding-agent skills across multiple tools.
- `agents/` is the canonical user-skill folder and maps to `~/.agents/skills`.
- `claude/` is a required mirror of `agents/` and maps to `~/.claude/skills`. Claude Code does not read `~/.agents/skills`, so skills you want Claude to use must also live here.
- `codex/`, `copilot/`, `cursor/`, `gemini/` are optional per-agent folders. Users opt into these during setup.
- The repo is intentionally blank in the top-level agent folders. The template ships one project-local skill, `agent-skills-setup-manager`, under `.agents/skills/` and `.claude/skills/`, which manages this repo itself.

## Source-of-truth Rules

- New skills go into `agents/` first.
- By default a new skill should also be installed into `claude/` so Claude Code can use it.
- **User preference (set 2026-08-17): always install new skills into both `agents/` and `claude/` without asking.** Do not re-prompt for this choice.
- Avoid putting user skills into `codex/`, `copilot/`, `cursor/`, or `gemini/` unless the user explicitly says so. Those folders are for agents whose system directory layout is separate from `~/.agents/skills`.
- `opencode` needs no dedicated folder — it reads `~/.agents/skills` and `~/.claude/skills` directly per the open Agent Skills / SKILL.md standard, so anything installed into `agents/`+`claude/` already covers it.

## Installed Third-Party Skills (provenance)

Installed 2026-08-17 from public repos, curated for quality/maintenance/breadth (not by star count — several source repos show star counts inconsistent with their age, a known bot-star pattern; curation here was by content review). Update by re-pulling from source and re-copying into both `agents/` and `claude/`.

| Skills | Source |
| --- | --- |
| code-review, codebase-design, diagnosing-bugs, domain-modeling, implement, improve-codebase-architecture, prototype, research, resolving-merge-conflicts, tdd, to-spec, to-tickets, triage, wayfinder, wizard, grilling, handoff, teach, to-questionnaire, wait-what, writing-for-agents, git-guardrails-claude-code, setup-pre-commit | [mattpocock/skills](https://github.com/mattpocock/skills) |
| algorithmic-art, brand-guidelines, canvas-design, claude-academy-guide, claude-api, discernment-nudge, doc-coauthoring, docx, frontend-design, internal-comms, mcp-builder, pdf, pptx, skill-creator, slack-gif-creator, theme-factory, web-artifacts-builder, webapp-testing, xlsx | [anthropics/skills](https://github.com/anthropics/skills) |
| watch | [bradautomates/claude-video](https://github.com/bradautomates/claude-video) |
| last30days | [mvanhorn/last30days-skill](https://github.com/mvanhorn/last30days-skill) |
| humanizer | [blader/humanizer](https://github.com/blader/humanizer) |
| ponytail, ponytail-review, ponytail-audit, ponytail-debt, ponytail-gain, ponytail-help | [DietrichGebert/ponytail](https://github.com/DietrichGebert/ponytail) |
| investigate-first, lean-build, safe-refactor, surgical-patch, verify-and-stop | [juliusbrussee/caveman](https://github.com/juliusbrussee/caveman) (cherry-picked; skipped the caveman-speak persona skills and bundled binaries) |
| tdd-workflow, security-review, verification-loop, mcp-server-patterns | [affaan-m/ECC](https://github.com/affaan-m/ECC) (cherry-picked from ~350 skills; used the canonical `skills/` copies, not the `.kiro/`/`.cursor/`/`.agents/`/translated mirrors) |
| adhd | [uditakhourii/adhd](https://github.com/uditakhourii/adhd) — divergent-ideation skill for brainstorming/design decisions, not for routine bugs |

**Known naming collisions with Claude Code's built-in skills:** `code-review` (mattpocock/skills — two-axis Standards-vs-Spec review) and `security-review` (affaan-m/ECC) both share names with built-in Claude Code plugin skills. They are different implementations — if Claude picks the wrong one or behavior seems off, that's why. Rename or remove one if it causes confusion.

**Not installed (considered, skipped):** akshat1404/the-human-touch (redundant with humanizer), github/awesome-copilot (425-skill index, browse selectively), anthropics/claude-plugins-official (MCP service integrations, not general skills), sickn33/agentic-awesome-skills (noisy scraped catalog), shanraisshan/claude-code-best-practice (toy/demo skills). The rest of caveman and ECC (persona/novelty skills, ~340 narrow ECC verticals) were also left out. Ask the user if they want any of these added later.

## Repo Shape

- `setup_symlinks.py`: interactive setup script. Validates, backs up, imports, and symlinks the selected agent directories. Idempotent — safe to re-run.
- `.agent-skills-setup/config.json`: persists the user's agent selection.
- `.agent-skills-setup/backup/`: per-agent backups created during setup.
- `.agent-skills-setup/backup/README.md`: recovery instructions written before live changes begin.
- `.agents/skills/agent-skills-setup-manager/` and `.claude/skills/agent-skills-setup-manager/`: the project-local skill that helps agents drive setup, repair, status checks, and re-runs. These hidden folders are scoped to this repo (project-level skills) and are not symlinked anywhere.

## Development Commands

- `python3 setup_symlinks.py --dry-run`: preview validation, backup, import, and symlink actions.
- `python3 setup_symlinks.py`: run the interactive setup flow.
- `python3 setup_symlinks.py --status`: print the current symlink topology and saved selection.
- `python3 setup_symlinks.py --agents=agents,claude,codex`: run non-interactively with a specific agent selection (`agents` and `claude` are always included).

## Important Notes

- The script is idempotent. Already-configured agents (system path is a symlink into the matching repo folder) are skipped automatically.
- The script keeps simple, readable logic: no force mode, no destructive shortcuts. It refuses to touch foreign symlinks or non-directory paths.
- Backups under `.agent-skills-setup/backup/<agent>/` are important and are not auto-deleted.
- After setup, edits inside the repo folders or the system skill directories both modify the same files.

## Validation

- Use `python3 setup_symlinks.py --dry-run` first when changing setup logic.
- After a real setup, each configured system skill directory should resolve to the matching repo folder.
- `python3 setup_symlinks.py --status` is a fast way to confirm the topology.
