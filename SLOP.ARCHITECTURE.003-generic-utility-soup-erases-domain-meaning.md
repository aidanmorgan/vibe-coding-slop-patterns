# SLOP.ARCHITECTURE.003 — Generic utility soup (erases domain meaning)

**Family:** ARCHITECTURE

## Summary

This slop turns everything into `utils.py` and passes around untyped dicts. Domain concepts lose
meaning, invariants aren’t expressed, and the system becomes a pile of “helpers” that do unrelated
things.  AI code often does this because generic helpers are easy to invent and hard to falsify in
review.


## Examples (Python)

```python
def handle(data):
    # does validation, persistence, and formatting all in one
    data["x"] = data.get("x", 0) + 1
    save(data)
    return format(data)
```

```python
def process(x: dict) -> dict:
    # x is dict-of-anything
    x["status"] = "ok"
    return x
```

```python
def util(a, b, c):
    # vague name, unclear responsibility
    return (a, b, c)
```

## Remediation

- Introduce domain types (dataclasses/pydantic models) and make invalid states unrepresentable.
- Ban “any-dict everywhere” in core layers; require typed boundaries.
- Split utilities by responsibility and domain; avoid catch-all modules.
- Improve naming: functions should describe intent, not mechanics.
