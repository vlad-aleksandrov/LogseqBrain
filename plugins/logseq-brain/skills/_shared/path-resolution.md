# Graph Path Resolution

All four brain skills (`brain-init`, `brain-load`, `brain-save`, `brain-status`) need to resolve the user's ClaudeBrain Logseq graph folder before doing anything else. Resolution branches by host.

## Resolution order

### In Cowork (desktop app)

If no folder is currently connected, call `request_cowork_directory` to ask the user to select their ClaudeBrain graph folder. Store the resolved path for the session.

### In Claude Code / GitHub Copilot CLI / Gemini CLI

Try in this order; stop at the first success:

1. **Argument:** the user provided a path inline (e.g., "load brain at /path/to/graph"). Use it directly.
2. **Environment variable:** read `LOGSEQ_BRAIN_PATH`. If set and the path exists, use it.
3. **Config file (XDG location):** read `~/.config/logseq-brain/config.json` (Linux/macOS) or `%APPDATA%\logseq-brain\config.json` (Windows). If it exists and contains `{"graphPath": "..."}` and the path exists, use it. This location survives plugin reinstalls and version bumps.
   - Legacy fallback: if the XDG file is absent, also check `.brain-config.json` in the plugin base directory. If found, warn the user once: "Config is at the old plugin-root location — move it to `~/.config/logseq-brain/config.json` to survive future upgrades." Then use the value.
4. **Ask the user.** Prompt: "Where is your ClaudeBrain Logseq graph folder?"

Once resolved, all other brain operations in this session use that path.

## Config file shape

`~/.config/logseq-brain/config.json` supports these keys:

```json
{
  "graphPath": "/absolute/path/to/ClaudeBrain",
  "journeyLog": true,
  "gitAutoPush": false
}
```

- `graphPath` (string, required for non-Cowork hosts): absolute path to the graph folder.
- `journeyLog` (boolean, optional, default `true`): whether to write activity-trail entries on each brain skill use. See `skills/_shared/journey-log.md`.
- `gitAutoPush` (boolean, optional, default `false`): if `true`, brain-save will `git push` after every commit.

Create with:
```bash
mkdir -p ~/.config/logseq-brain
echo '{"graphPath": "/absolute/path/to/ClaudeBrain"}' > ~/.config/logseq-brain/config.json
```

## Failure modes

- **Path doesn't exist:** tell the user the path is invalid and ask for a correct one. Don't try to create the folder — that's `brain-init`'s job once the path is confirmed.
- **Empty folder:** that's not a path-resolution failure; it's a signal that `brain-init` first-time setup is needed. Hand off to `brain-init` if the user wasn't already running it.
- **Conflicting config + env:** env var wins (per the resolution order above).
