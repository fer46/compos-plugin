---
name: orientation
description: Use before modifying, refactoring, adding, or removing code — fires on structural changes, new features, dependency changes, and service boundary modifications. Skip for single-function edits, test-only changes, docs, formatting, config tweaks.
---

# Orientation

Query the architectural map before implementing changes. Surfaces constraints you must not violate, risks in the affected area, and decisions that explain why the system is the way it is.

## Bootstrap Gate

If no `.compos/` directory exists in the project root:

1. Ask the user: "This project doesn't have an architectural map yet. Want me to scan and initialize it?"
2. On confirmation:
   - If `compos` CLI is available: run `compos init && compos analyze`
   - Otherwise: explore the codebase, use `register_component` and `register_relationship` to build the initial map
3. Proceed with orientation below.

## Flow

1. Identify file paths involved in the requested change.

2. Query context:
   ```
   get_component_context(file_paths=["path/to/file1.py", "path/to/file2.py"])
   ```

3. If the change is structural (new service, changed dependency, moved module):
   ```
   get_blast_radius(file_path="path/to/file.py", change_type="modify")
   ```
   Change types: `modify` (downstream), `remove` (all directions), `replace` (downstream).

4. Present concisely:
   - Components and responsibilities in the affected area
   - **Hard constraints** — highlight these, they must not be violated
   - Soft constraints — note but don't block on these
   - Active risks
   - Past decisions — "why is it this way?" context
   - Unresolved file paths (files not yet in the map)

## File Path Resolution

Pass file paths directly via the `file_paths` parameter. No ID lookup needed. Use component IDs only for infrastructure components (databases, external APIs) with no local files.

If some paths resolve and others don't, present context for resolved components and note unmapped files. The architectural-logging skill handles registration after implementation.

## Do NOT

- Block the user from proceeding
- Repeat context already in the conversation
- Call `list_components` or `get_system_map` — use targeted `file_paths` queries
- Call `evaluate_proposed_change` — that's preflight-check's job
