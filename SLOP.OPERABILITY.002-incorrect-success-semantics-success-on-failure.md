# SLOP.OPERABILITY.002 — Incorrect success semantics (success on failure)

**Family:** OPERABILITY

## Summary

This slop returns success even when the operation failed. It creates phantom correctness: UIs say
“done,” users proceed, and the system later fails in reconciliation or support tickets.  It often
comes from broad try/except blocks that return a default response to “avoid breaking the flow.”


## Examples (Python)

```python
def handler():
    try:
        do_work()
    except Exception:
        return {"ok": True}  # lies
    return {"ok": True}
```

```python
def create():
    try:
        db.insert()
    except Exception:
        return 200, {"status": "created"}
```

```python
def update_profile():
    try:
        update()
    except Exception:
        return {"updated": True}
```

## Remediation

- Return correct status and error contracts; do not lie to the caller.
- Make clients handle failures explicitly (retry UI, show actionable error).
- Add tests for failure paths; verify non-200 responses and error payloads.
- Record failed operations for debugging and alerting, not silent success.
