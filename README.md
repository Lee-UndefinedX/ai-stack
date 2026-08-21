# ai-stack

A portable AI configuration repo: MCP servers and skills, ready for any AI tool on a new machine to apply automatically.

## Usage on a new machine

Send the following line to the AI tool on the new machine (CodeBuddy / Claude Code / Cursor, etc.):

> Initialize my AI environment using https://github.com/Lee-UndefinedX/ai-stack.git

The AI tool will then:

1. Clone the repo to `~/ai-stack`
2. Read `skills/init/SKILL.md` and execute it on its own
3. Detect the current AI tool (CodeBuddy / Claude Code / Cursor) and symlink the skills into its skills dir
4. Merge the MCP servers from `mcp/servers.json` into the tool's local MCP config (existing config is preserved)

### About tokens

- Provide your GitHub token **in the conversation** when needed (for GitHub access). No upfront configuration required.
- The AI tool must never write the token into any config file or commit it to the repo.

## Structure

```
ai-stack/
├── mcp/servers.json       # MCP server configs (merged into each tool's local config)
├── skills/                # skills (symlinked into each tool)
│   ├── init/              # new-machine bootstrap, executed by the AI itself
│   └── chrome-devtools-mcp-setup/
└── memory/                # memory (not yet populated)
```

## Updates

```bash
cd ~/ai-stack && git pull
```

The repo is public; cloning needs no login.
