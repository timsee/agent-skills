# Not in the docs

- **Keys are environment references, never values.** The docs show literal key values; write a reference to `DD_API_KEY` and `DD_APP_KEY` instead, in the client's syntax:
  - Claude Code: `'DD-API-KEY: ${DD_API_KEY}'` (single quotes, so the name lands in the file).
  - OpenCode: `"DD-API-KEY": "{env:DD_API_KEY}"`.
  - Codex: see step 4 of SKILL.md.
- **Claude Code plugin:** it reads `DD_MCP_DOMAIN` and `DD_MCP_TOOLSETS` from Claude Code's launch environment, and a nonempty value there wins over any default you edit, even after `/reload-plugins`. Check for them first; if set, ask the user to change them and restart Claude Code.
