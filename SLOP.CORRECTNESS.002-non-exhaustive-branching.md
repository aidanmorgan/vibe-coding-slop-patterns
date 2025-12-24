# SLOP.CORRECTNESS.002 — Non-exhaustive branching

**Family:** CORRECTNESS

## Summary

Non-exhaustive branching happens when code handles only the currently known cases and silently
ignores new ones. This is common in AI output because it mirrors examples and doesn’t anticipate
future expansion or “unknown” values.  The result is brittle logic that fails quietly when new
states are introduced (new enum values, new event types, new workflow statuses).


## Examples (Python)

```python
def handle_event(evt: dict) -> str:
    if evt["type"] == "created":
        return "ok"
    if evt["type"] == "deleted":
        return "ok"
    # missing else => returns None
```

```python
def priority_label(p: str) -> str:
    if p == "high":
        return "H"
    elif p == "low":
        return "L"
    # missing medium/unknown
```

```python
def parse_kind(kind: str) -> int:
    mapping = {"A": 1, "B": 2}
    return mapping[kind]  # KeyError on new kind
```

## Remediation

- Always define behavior for unknown values (fail closed with a clear exception).
- Centralize mappings and enforce defaults; avoid scattered if/elif ladders.
- Add tests that feed unknown/new values and assert safe behavior.
- When values are controlled, prefer explicit enums and exhaustive validation at boundaries.
