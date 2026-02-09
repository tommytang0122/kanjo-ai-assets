# kanjo-ai-assets

A category-centric collection of Claude Code skills, MCP servers, and plugins.

## Structure

### `skills/`

Claude Code skills defined by a `SKILL.md` file (frontmatter + instructions). Each skill lives in its own subdirectory and may include supporting files like templates, scripts, or examples.

### `mcp-servers/`

MCP (Model Context Protocol) server implementations. Each server lives in its own subdirectory with its own build configuration (`pyproject.toml` for Python, `package.json` for TypeScript, etc.).

### `plugins/`

Claude Code plugins that bundle skills, MCP servers, and hooks together. Each plugin has a `manifest.json` and may contain its own `skills/`, `mcp-servers/`, and `hooks/` subdirectories.

## Adding a new item

1. Create a subdirectory under the appropriate category (`skills/`, `mcp-servers/`, or `plugins/`).
2. Follow the conventions for that category (see structure above).
3. Include a README or SKILL.md explaining what the item does and how to use it.

## License

[MIT](LICENSE)
