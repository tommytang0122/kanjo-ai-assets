# CLAUDE.md

This file provides guidance to Claude Code (claude.ai/code) when working with code in this repository.

## Project Overview

**kanjo-ai-assets** is a category-centric collection of Claude Code skills, MCP servers, and plugins (MIT license).

## Repository Structure

```
skills/<skill-name>/         # Claude Code skills (SKILL.md-based)
mcp-servers/<server-name>/   # MCP server implementations
plugins/<plugin-name>/       # Bundled plugins (skills + servers + hooks)
```

## Language & Tooling

This is a mixed-language repository. Individual items may use Python, TypeScript, or other languages, each with their own build configuration:

- **Python items**: use `pyproject.toml` with `uv` as the package manager
- **TypeScript items**: use `package.json` with `npm` or similar

There is no top-level build system. Each item is self-contained.

## Conventions

- Each skill must have a `SKILL.md` with frontmatter and instructions.
- Each MCP server must have a `README.md` and its own build config (`pyproject.toml` or `package.json`).
- Each plugin must have a `manifest.json` and may contain `skills/`, `mcp-servers/`, and `hooks/` subdirectories.
- Keep items self-contained within their subdirectory.
