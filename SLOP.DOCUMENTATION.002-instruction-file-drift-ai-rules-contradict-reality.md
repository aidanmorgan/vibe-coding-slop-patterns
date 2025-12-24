# SLOP.DOCUMENTATION.002 — Instruction-file drift (AI rules contradict reality)

**Family:** DOCUMENTATION

## Summary

Teams increasingly add “AI instruction” files, style guides, and agent prompts to repos. These rot
quickly because they’re not executed. When they drift, they mislead both humans and tools—causing
repeated bad suggestions and wasting cycles.  The result is a self-reinforcing loop: stale
instructions create bad outputs, which create more instructions to patch the bad outputs.


## Examples (Python)

```python
# copilot_instructions.md content represented in Python
INSTRUCTIONS = """
Use ORM X for all database access.
Never use raw SQL.
"""
# repo actually uses raw SQL extensively, causing tools to suggest wrong patterns
```

```python
RULES = """
Always name the main module app.py
"""
# project has moved to a package layout, but tools still suggest app.py modifications
```

```python
PROMPT = """
Prefer synchronous requests for simplicity.
"""
# contradicts codebase which is async-first
```

## Remediation

- Treat instruction files as code: version them, assign owners, and require review for changes.
- Keep instructions minimal and precise; delete outdated guidance aggressively.
- Schedule periodic audits to remove drift and contradictions.
- Prefer tool-enforced rules (lint/CI) over long narrative instruction documents.
