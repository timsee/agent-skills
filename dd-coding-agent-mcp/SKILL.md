---
name: dd-coding-agent-mcp
description: Set up the user's coding agents (Claude Code, Codex, Gemini CLI, OpenCode, Cursor, Copilot CLI) for Datadog by connecting the Datadog MCP server with the right toolsets and installing skills. Use when the user wants Datadog tools in their agent, or another skill needs missing MCP tools or has a skill to install. Not for building MCP servers.
metadata:
  version: "0.4.0"
  author: datadog-labs
  repository: https://github.com/datadog-labs/agent-skills
  tags: datadog,mcp,skills,onboarding,coding-agent
  status: beta
---

# Set up coding agents for Datadog

Do what the user or calling skill asks: connect the MCP server, install skills, or both. Either way you only write config and copy files, which takes seconds, never waits on the user, and needs no Datadog MCP tools in your own session. Their reload and any sign-in come later.

## Connect the MCP server

1. **Find the user's coding agents.** Check for each client's CLI on `PATH` (`claude`, `codex`, `gemini`, `opencode`, `cursor-agent`, `copilot`) and its config (`~/.claude.json`, `~/.codex/`, `~/.gemini/`, `~/.config/opencode/`, `~/.cursor/`, `~/.copilot/`). Configure every one you find, starting with the one you're running in. If the user named one, configure only that.
2. **Toolsets.** If the user or calling skill names toolsets, use exactly that list, adding `core` if it's missing. Otherwise use `all`. Preview toolsets such as `live-debugger` and `bits-release` load only when named.
3. **Read the docs.** Fetch https://docs.datadoghq.com/bits_ai/mcp_server/setup/ and follow each client's section. Prefer the client's Datadog plugin when the docs offer one. Also read `references/not-in-docs.md`.
4. **Configure** each client for the user's site (`DD_SITE`, else ask). The endpoint is `https://mcp.<site>/v1/mcp`. Edit an existing Datadog entry instead of adding a second one, and keep the user's other servers and settings. Use sign-in when a person has a browser; use keys only when `DD_API_KEY` and `DD_APP_KEY` are already set or the user asks for them. **Codex:** ignore the docs' key headers. Codex sends `http_headers` as literal text, so keys there either leak into the file or fail with 401. Write the entry as exactly:

   ```toml
   [mcp_servers.datadog]
   url = "https://mcp.<site>/v1/mcp"
   http_headers = { "X-Datadog-MCP-Toolsets" = "<toolsets>" }

   [mcp_servers.datadog.env_http_headers]
   "DD-API-KEY" = "DD_API_KEY"
   "DD-APPLICATION-KEY" = "DD_APP_KEY"
   ```
5. **Check.** Run each client's MCP list command and confirm the Datadog entry is there. On the keys path, also confirm the server accepts it (HTTP `200`, not `401`/`403`):

   ```sh
   curl -s -o /dev/null -w '%{http_code}\n' -X POST "<URL>" \
     -H "DD-API-KEY: $DD_API_KEY" -H "DD-APPLICATION-KEY: $DD_APP_KEY" \
     -H 'Content-Type: application/json' -H 'Accept: application/json, text/event-stream' \
     -d '{"jsonrpc":"2.0","id":1,"method":"initialize","params":{"protocolVersion":"2025-06-18","capabilities":{},"clientInfo":{"name":"setup-check","version":"1"}}}'
   ```

## Install skills

You get either a skill to copy or the names of datadog-labs skills. Install into every client from step 1 of **Connect the MCP server** (find them the same way even if you're only installing).

- **A skill to copy:** a file such as another skill's `assets/skills/<name>.md`, a folder, or text in the conversation. Its `name` frontmatter field is `<name>`. Copy it without rewording: a file or text becomes `<name>/SKILL.md`, a folder is copied whole. Where shell copies aren't allowed, write each file with your file tool. Replace an existing `<name>/` and say so.

  | Client | User skills folder |
  |---|---|
  | Claude Code | `~/.claude/skills/<name>/` |
  | Codex, Gemini CLI, OpenCode, Cursor, Copilot CLI | `~/.agents/skills/<name>/` |

  Use the project's `.claude/skills/` or `.agents/skills/` only if the user asks.
- **datadog-labs skills by name:** `npx -y skills add datadog-labs/agent-skills --skill <name> [--skill <name> ...] -a <agent> -g -y`, where `<agent>` is `claude-code`, `codex`, `gemini-cli`, `opencode`, `cursor`, or `github-copilot`. Skip a client that already has the skill.

Then check that each `<name>/SKILL.md` exists and starts with its frontmatter. Where the client lists skills, confirm it's there: `gemini skills list`, `opencode debug skill`.

## Report

The toolsets, then one line per client you checked: MCP configured (and the file or command), the skills installed and where, or what wasn't done with the concrete reason: the command and its error, or what was missing. "Couldn't run it" isn't a reason. Then the user's remaining reload or sign-in steps (new skills and MCP tools load in the next session). Say "verified" only for checks you ran and saw pass. Never print a key value. A calling skill continues either way.
