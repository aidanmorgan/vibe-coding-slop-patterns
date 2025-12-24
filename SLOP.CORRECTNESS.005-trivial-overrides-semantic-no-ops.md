# SLOP.CORRECTNESS.005 — Trivial overrides / semantic no-ops

**Family:** CORRECTNESS

## Summary

In Python this shows up as meaningless wrappers and “identity” methods that pretend to implement
behavior but simply pass through, often generated to satisfy a request for “clean architecture” or
“a well-designed interface.”  The harm is subtle: callers assume semantics exist (validation,
normalization, hashing) when the function does nothing. This creates bugs that are hard to trace
because the code looks intentionally structured.


## Examples (Python)

```python
def normalize_email(email: str) -> str:
    # supposedly normalizes, but does nothing
    return email
```

```python
class Token:
    def verify(self) -> bool:
        # looks like a verification method, but always defers
        return super().verify()  # type: ignore[attr-defined]
```

```python
def validate(payload: dict) -> dict:
    # “validation” function that just returns input unchanged
    return payload
```

## Remediation

- Don’t add abstraction methods unless they add behavior or enforce invariants.
- If a wrapper exists, encode its contract in tests (e.g., normalization actually lowercases and trims).
- Prefer explicit, tested helper functions over “architecture-looking” no-op layers.
- Remove dead wrappers; they are worse than missing code because they mislead reviewers.
