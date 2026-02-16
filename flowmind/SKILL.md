---
name: flowmind
description: "Workflow chaining engine. Define multi-step flows in YAML and execute them sequentially or in parallel."
metadata: { "openclaw": { "emoji": "🔗", "always": true } }
---

# Flowmind

Workflow chaining engine. Reads YAML flow definitions and executes steps in order.

## Usage

- `/flowmind run <name>` — Run a named workflow
- `/flowmind list` — List available workflows
- `/flowmind show <name>` — Show workflow definition

## Flow Directory

Flows are stored in `~/.openclaw/workspace/flows/` as YAML files.

## Flow Format

```yaml
name: Flow display name
description: What this flow does
steps:
  - skill: skill-name        # Invoke an OpenClaw skill
    args: optional arguments
  - action: "free text"       # Execute a free-text instruction
  - parallel:                 # Run steps in parallel
      - skill: skill-a
      - skill: skill-b
```

## Execution Rules

1. **Read the YAML file** from `~/.openclaw/workspace/flows/<name>.yaml`
2. **Execute each step in order:**
   - `skill:` — Invoke the named skill (like typing `/skill-name args`)
   - `action:` — Perform the described action directly
   - `parallel:` — Execute all sub-steps concurrently, wait for all to complete
3. **Report results** after each step completes
4. **If a step fails**, report the error and ask the user whether to continue or abort

## Commands

### `run <name>`

```bash
cat ~/.openclaw/workspace/flows/<name>.yaml
```

Parse the YAML and execute each step. Between steps, provide a brief status update.

### `list`

```bash
ls ~/.openclaw/workspace/flows/*.yaml 2>/dev/null
```

Show each flow's name and description.

### `show <name>`

```bash
cat ~/.openclaw/workspace/flows/<name>.yaml
```

Display the flow definition without executing it.

## Error Handling

- If a flow file doesn't exist, say so and list available flows
- If a skill referenced in a step doesn't exist, skip it and warn
- Always complete as many steps as possible before reporting failures
