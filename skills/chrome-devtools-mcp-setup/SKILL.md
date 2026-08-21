---
name: chrome-devtools-mcp-setup
description: Set up and troubleshoot the chrome-devtools MCP server locally (enable Chrome remote debugging, configure, verify connection).
---

# chrome-devtools MCP Setup

## Prereqs

- Chrome Dev installed at `/Applications/Google Chrome Dev.app`

## Steps

1. **Enable remote debugging in Chrome Dev**: open `chrome://inspect/#remote-debugging`, click to enable
2. **Add config** to `~/.codebuddy/mcp.json`:
   ```json
   {
     "mcpServers": {
       "chrome-devtools": {
         "command": "npx",
         "args": ["-y", "chrome-devtools-mcp@latest", "--autoConnect", "--channel=dev"]
       }
     }
   }
   ```
3. **Restart CodeBuddy** so the MCP server starts
4. **Verify**: use `list_pages` — should list pages in the running Chrome Dev

## Notes

- `--autoConnect` is **lazy**: server starts fine without Chrome; it only connects when a tool is actually called
- Chrome must be running with remote debugging enabled **before** calling tools
- Use `--browser-url=http://127.0.0.1:9222` instead if you prefer a manually-launched Chrome (e.g. `--remote-debugging-port=9222 --user-data-dir=<non-default-dir>`)
- Old Chrome-Debug.app approach is deprecated
