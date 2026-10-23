<p align="center">
  <img src="../../assets/banner.png" alt="agentmemory" width="640" />
</p>

<h1 align="center">
  &nbsp;agentmemory for Oh My Pi (omp)
</h1>

<p align="center">
  <strong>Your omp sessions remember everything. No more re-explaining.</strong><br/>
  <sub>Persistent cross-session memory via <a href="https://github.com/rohitg00/agentmemory">agentmemory</a> — shared with Claude Code, Codex CLI, Gemini CLI, Hermes, OpenClaw, pi, and more.</sub>
</p>

---

## Quick setup

Start the agentmemory server in a separate terminal:

```bash
npx @agentmemory/agentmemory
```

Tested with Oh My Pi 18.12.6. Run this from an agentmemory checkout to link the extension into omp's plugin directory:

```bash
omp plugin install ./integrations/omp
```

Then restart omp for the extension to load. Verify it was picked up:

```bash
omp plugin doctor
# ✔ plugins_directory: Found at ...
# ✔ plugin:agentmemory-omp-extension: ...
```

Alternatively, `omp install ./integrations/omp` is an alias for the same command.

> **Note:** Use `omp plugin install` only. Do **not** copy this folder to
> `~/.omp/agent/extensions/agentmemory/` — omp scans that directory too, so the
> extension would load twice and every tool call would be observed twice.

## What it adds

- `memory_health` — confirm the shared memory server is reachable
- `memory_search` — search prior decisions, bugs, workflows, and preferences
- `memory_save` — write durable facts back to long-term memory
- `/agentmemory-status` — check health from inside omp
- Lifecycle hooks recall project memories and lessons, capture prompts and tool outcomes, track session switches, and preserve memory context during compaction.
- Tool outputs remain intact. Memory capture is bounded separately from the conversation sent to the model.

## Environment variables

| Variable | Default | Description |
|---|---|---|
| `AGENTMEMORY_URL` | `http://localhost:3111` | agentmemory server URL |
| `AGENTMEMORY_SECRET` | (auto-detected locally) | Explicit bearer token; loopback connections otherwise read `~/.agentmemory/.env` or `~/.agentmemory/secret`. Remote servers require an explicit token. |
| `AGENTMEMORY_CREDENTIAL_MODE` | (unset) | Set to `explicit` to disable local credential discovery. |
| `AGENTMEMORY_PROJECT_NAME` | (unset) | Overrides the project name used in session/observation payloads. Defaults to the git repo basename (or cwd basename if not a git repo). |
| `AGENTMEMORY_REQUIRE_HTTPS` | (off) | When set to `1`, refuse to send a bearer token over plaintext HTTP to a non-loopback host. Sends the token only when `AGENTMEMORY_URL` is `https://...` or points at `localhost`/`127.0.0.1`/`::1`. With this off, the plugin warns once but still sends. |
| `AGENTMEMORY_INJECT_CONTEXT` | (off) | When set to `1`, injects file enrichment context (via `/enrich`) into the conversation for files touched by tools. |

## Smoke test

Run omp and ask it to use the `memory_health` tool, or call the command directly:

```text
/agentmemory-status
```

You should see `agentmemory healthy` and a footer status like `🧠 agentmemory`.

## Notes

- This extension uses omp's extension API, not MCP. Session finalization is awaited within omp's two-second shutdown budget; summary refresh and scheduled consolidation run on the server.
- If the daemon is unavailable, the conversation continues and memory tools report failure. Unacknowledged captures are not queued for offline replay.
- One local agentmemory server can be shared across omp, pi, pi2, Hermes, OpenClaw, Claude Code, Codex CLI, and Gemini CLI.

## See also

- [agentmemory main README](../../README.md)
- [pi integration](../pi/README.md)
- [Hermes integration](../hermes/README.md)
- [OpenClaw integration](../openclaw/README.md)
