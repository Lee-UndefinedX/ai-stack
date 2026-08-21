---
name: init
description: Set up this ai-stack repo on a new machine — clone the repo, then auto-wire skills/memory/mcp into the current AI tool. Execute all steps yourself; do not wait for the user.
---

# ai-stack init (executed by the AI tool)

You are the AI tool on a new machine. The user gave you the git URL of this repo (and a GitHub token if needed). **Execute all steps below yourself** — do not wait for the user. Goal: make this repo's skills and MCP configs active for the current AI tool.

## 0. Preflight

- Check that `git` is available (`git --version`). If not, tell the user to install it (macOS: `brew install git`) and continue once installed.
- Note the git URL the user provided; if a token was also provided, handle it per the security rule in step 5.

## 1. Get the repo at the fixed location `~/ai-stack`

- If `~/ai-stack` does not exist: `git clone <git-url> ~/ai-stack`
- If it exists: `cd ~/ai-stack && git pull`
- Confirm `~/ai-stack` contains `skills/` and `mcp/servers.json`; otherwise abort and report.

## 2. Detect the current AI tool and pick the target dir

Check in order, by directory existence:

| Tool | Probe dir | Wiring root |
|------|-----------|-------------|
| CodeBuddy | `~/.codebuddy` | `~/.codebuddy` |
| Claude Code | `~/.claude` | `~/.claude` |
| Cursor | `~/.cursor` | `~/.cursor` |

- Use the first match as the wiring root (`$TOOL`); if several match, wire them all.
- If none match, leave skills in the repo and tell the user to wire `~/ai-stack/skills` manually; then stop.

## 3. Wire skills

- For each tool: `mkdir -p $TOOL/skills`
- Symlink every subdir of `~/ai-stack/skills/` (except `init`, which is just the bootstrap) into `$TOOL/skills/`:
  ```bash
  ln -s ~/ai-stack/skills/<skill-name> $TOOL/skills/<skill-name>
  ```
- If a file/symlink with the same name already exists, skip it (do not overwrite).

## 4. Wire memory (optional)

- Only if `~/ai-stack/memory` exists and is non-empty; otherwise skip this step.
- `mkdir -p $TOOL/memory`, then `ln -s ~/ai-stack/memory $TOOL/memory` (skip if already present).

## 5. Merge MCP config

- Read the tool's existing MCP config file:
  - CodeBuddy: `~/.codebuddy/mcp.json` (key `mcpServers`)
  - Claude Code: `~/.claude.json` (key `mcpServers`)
  - Cursor: `~/.cursor/mcp.json` (key `mcpServers`)
  - Create the file if it does not exist.
- Read `mcpServers` from `~/ai-stack/mcp/servers.json` and merge in any server **not already present** in the tool's config; keep existing servers untouched.
- Write back valid JSON (preserve formatting); do not alter existing content.
- **Token security rule**: never write the user's token into mcp.json or any config file, or any file that could be committed. If the user needs GitHub access, ask them to configure it via the tool's own secret/env mechanism, or use it ad-hoc in the conversation.

## 6. Verify and report

- `ls ~/ai-stack` to confirm the structure is intact.
- Confirm the skills symlinks are in place (targets exist under `$TOOL/skills` and point correctly).
- Confirm the MCP config now contains the merged server (`chrome-devtools`).
- Report to the user: which tool was wired, which skills, and the MCP merge result. If the tool needs a restart/reload to pick up the config, say so.

## Notes

- The repo is public; cloning needs no login.
- To update: `cd ~/ai-stack && git pull`.
- Symlinks are preferred over copying so repo updates flow through.
- This skill covers new-machine setup; pushing changes back to the repo is out of scope.
