---
name: architectural-logging
description: Use after creating new modules, changing imports or dependencies between components, adding services, modifying infrastructure, or restructuring code. Skip for edits within a single function, test changes, docs, formatting.
---

# Architectural Logging

Keep the architectural map current by registering structural changes after implementing them. Operate autonomously — update the map directly, report what you did in one line.

## Bootstrap Gate

If no `.compos/` directory exists in the project root:

1. Ask the user: "This project doesn't have an architectural map yet. Want me to scan and initialize it?"
2. On confirmation:
   - If `compos` CLI is available: run `compos init && compos analyze`
   - Otherwise: explore the codebase, use `register_component` and `register_relationship` to build the initial map

## What Counts as Architectural

Log these changes:
- New file representing a distinct unit of responsibility
- New dependency between modules that crosses component boundaries
- Changed or removed service, database, queue, or external API integration
- New API endpoint representing a new capability boundary
- Moved or renamed module changing component structure

**Do NOT log:** new utility/helper files, edits within a function, test files, documentation, type definition files, config tweaks.

## Flow

After implementing structural changes:

1. **New component?**
   ```
   register_component(id="cache-service", name="Cache Service",
     responsibility="Response caching", type="service",
     paths=["src/cache/"])
   ```
   `paths` = files/directories this component owns (for future file path resolution).

2. **New cross-component dependency?**
   ```
   register_relationship(id="rel-api-cache", source="api-gateway",
     target="cache-service", type="dependency", pattern="synchronous",
     description="API reads cached responses")
   ```

3. **Changed component?**
   ```
   update_component(id="api-gateway", technology="FastAPI + Redis")
   ```

4. **Removed module?**
   ```
   remove_component(id="old-service", reason="Replaced by cache-service")
   ```

5. **Architectural decision worth preserving?**
   ```
   log_decision(id="dec-redis-over-memcached", title="Redis over Memcached",
     decision="Use Redis for caching", reasoning="Need pub/sub for invalidation",
     file_paths=["src/cache/client.py"],
     alternatives_considered=[{"option": "Memcached", "rejected_because": "No pub/sub support"}])
   ```

6. Report one-line summary:
   `"Map updated: +1 component (cache-service), +1 relationship (api->cache), decision logged (redis-over-memcached)"`

## Decision Logging Bias

**Decisions are the highest-value log entry.** Bias toward logging decisions. Components can be discovered later by the CLI analyzer. Reasoning evaporates between sessions — capture it now.

When you make a choice between alternatives (library, pattern, architecture), log it with `log_decision`. Include `alternatives_considered` with `rejected_because` — this is the most valuable data in the map for future sessions.

## Do NOT

- Register every new file as a component — only files with distinct responsibilities
- Ask the user for confirmation — operate autonomously, the map tracks provenance (`ai-annotated`), and human corrections always override
- Call read tools before logging — you just implemented the change, you have the context
