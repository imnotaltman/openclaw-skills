# OpenClaw Skills

This repo centralizes my OpenClaw skills and related automation assets. It is based on the currently active local setup in `~/.openclaw/workspace`.

## Included Skills

### `mission-control`
Morning briefing dashboard. Collects:
- Date/time
- Weather (Seoul)
- Recent iMessage chats
- Unread Apple Mail
- GitHub notifications and PR review requests
- Overnight hot topics
- System status (uptime, disk, memory)

### `flowmind`
Workflow chaining engine. Executes YAML flows stored under `~/.openclaw/workspace/flows/` with sequential and parallel steps.

### `night-worker`
Nightly autonomous worker. Reads a manifest file, executes pending tasks, retries once on failure, updates status, and sends minimal iMessage progress updates.

## Related Directories

- `skills/` — Skill definitions and scripts
- `flows/` — Flow definitions (YAML)
- `nightwork/` — Night-worker manifest and archive
- `scripts/` — Helper scripts

## How This Repo Is Structured

This repo is intended to mirror or export the skills and automation logic currently living in:

```
~/.openclaw/workspace
```

## Next Steps

- Export current `skills/`, `flows/`, and `nightwork/` into this repo
- Add a CI check for YAML validity
- Document the expected manifest schema
