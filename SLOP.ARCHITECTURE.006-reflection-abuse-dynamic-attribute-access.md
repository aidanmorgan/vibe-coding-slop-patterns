# SLOP.ARCHITECTURE.006 — Reflection abuse (dynamic attribute access)

**Family:** ARCHITECTURE

## Summary

AI assistants often resort to `getattr`, `setattr`, or `hasattr` to solve data mapping problems, treating Python objects like untyped dictionaries. This "reflection abuse" defeats IDE autocompletion, static type checking (mypy/pyright), and automated refactoring tools. The resulting code is brittle: renaming a field doesn't break the build but causes runtime crashes, and finding all usages of a property becomes a grep-based archaeological dig.

## Examples (Python)

```python
def update_user(user, updates: dict):
    # Dynamic update bypasses type safety and validation
    for key, value in updates.items():
        setattr(user, key, value)
```

```python
def get_status(obj):
    # "Safe" access that hides structural problems
    if hasattr(obj, "status"):
        return getattr(obj, "status")
    return None
```

```python
# Magic dispatch based on string names
method_name = f"handle_{event_type}"
getattr(handler, method_name)(payload)
```

## Remediation

- Prefer explicit property access. If you need dynamic mapping, use a dictionary or a typed mapping protocol.
- Use pattern matching (`match`/`case`) or explicit dispatch tables instead of `getattr` for dynamic dispatch.
- Enforce strict typing rules that flag `Any` or dynamic access on typed objects.
- If dynamic access is unavoidable (e.g., meta-programming), confine it to a small, well-tested kernel and expose a typed interface to the rest of the system.
