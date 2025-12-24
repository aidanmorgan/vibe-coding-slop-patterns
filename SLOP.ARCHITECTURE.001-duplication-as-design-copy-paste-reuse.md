# SLOP.ARCHITECTURE.001 — Duplication-as-design (copy/paste reuse)

**Family:** ARCHITECTURE

## Summary

LLMs are excellent at repeating patterns, which can become a codebase of near-duplicates: the same
validation logic copied into many handlers, each diverging slightly. This increases bug surface area
and makes changes expensive and inconsistent.  In vibe-coded environments, duplication spreads
quickly because copying is the easiest way to “add one more endpoint.”


## Examples (Python)

```python
def validate_user_payload(p: dict):
    if "email" not in p: raise ValueError("missing email")
    if "name" not in p: raise ValueError("missing name")
```

```python
def validate_order_payload(p: dict):
    # copied and slightly modified from validate_user_payload
    if "email" not in p: raise ValueError("missing email")
    if "items" not in p: raise ValueError("missing items")
```

```python
def validate_profile_payload(p: dict):
    # third copy with drift
    if "name" not in p: raise ValueError("missing name")
    if "bio" not in p: raise ValueError("missing bio")
```

## Remediation

- Extract shared logic once it appears twice; prefer composable validators over ad-hoc copies.
- Create canonical helpers/modules and require reuse for core invariants.
- Budget time for refactoring in the same sprint; duplication debt grows quickly.
- Add code review guidance: “If you’re copying a block, explain why it’s not shared.”
