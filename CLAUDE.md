# CLAUDE.md

## Project Overview

**kanjo-ai-assets** is a collection of Claude Code skills, MCP servers, and plugins, organized by category (MIT license).

## Repository Structure

```
skills/<category>/<skill-name>/   # Claude Code skills (SKILL.md + supporting files)
  superpowers/                    #   Dev workflow skills (TDD, debugging, code review, etc.)
  obsidian/                       #   Obsidian knowledge base skills
mcp-servers/<server-name>/        # MCP server implementations
plugins/<plugin-name>/            # Bundled plugins (skills + servers + hooks)
```

Skills are grouped into **category** subdirectories. Each skill lives in its own folder within a category.

## Language & Tooling

Mixed-language repo — no top-level build system. Each item is self-contained:

- **Python**: `pyproject.toml` + `uv`
- **TypeScript**: `package.json` + `npm`

## Conventions

- Every skill requires a `SKILL.md` with frontmatter (`name`, `description`, `triggers`) and instructions.
- Skills may include supporting files (templates, scripts, examples) in the same directory.
- MCP servers require a `README.md` and their own build config.
- Plugins require a `manifest.json` and may contain `skills/`, `mcp-servers/`, and `hooks/` subdirectories.
- Keep items self-contained within their subdirectory — no cross-references to files outside the item's folder.

## Installing Skills

To use a skill from this repo in a project, symlink or copy the skill's category folder into your project's `.claude/skills/` directory. Skills are registered via the `SKILL.md` frontmatter and invoked automatically based on their trigger conditions.

## Adding a New Skill

1. Pick or create a category under `skills/` (e.g., `skills/obsidian/`).
2. Create a subdirectory for the skill (e.g., `skills/obsidian/my-skill/`).
3. Add a `SKILL.md` with frontmatter and instructions.
4. Add any supporting files in the same directory.
