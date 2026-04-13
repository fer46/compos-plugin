# Compos — Architectural Memory Plugin

Persistent architectural memory for your codebase. Compos tracks components, relationships, constraints, risks, and decisions as you build — legible to humans, queryable by AI.

## Install

In Claude Code:

```
/install-plugin github:fer46/compos-plugin
```

**Prerequisite:** [uv](https://docs.astral.sh/uv/) installed on your machine.

## What's Included

- **MCP Server** — auto-configured via `uvx compos-mcp` (zero pre-install)
- **Orientation skill** — surfaces dependencies, constraints, and risks before you modify code
- **Architectural logging skill** — updates the architectural map after structural changes
- **Preflight check skill** — validates constraints before commits

## Optional: CLI

For faster bootstrapping and local analysis:

```bash
uv tool install compos-cli
compos init
compos analyze
```

## How It Works

Compos walks up from your current working directory to find your project root (looking for `.compos/` or `.git/`). Run Claude Code from inside your project and the plugin will find it automatically.

## Documentation

See the [Compos documentation](https://github.com/fer46/Compos) for full details.
