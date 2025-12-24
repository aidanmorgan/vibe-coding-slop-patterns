# SLOP.DOCUMENTATION.001 — Confident, wrong, or rotting docs/comments

**Family:** DOCUMENTATION

## Summary

AI-generated documentation often sounds authoritative even when it’s wrong. Docstrings, README
instructions, and comments can claim behavior that isn’t implemented, or drift over time as code
changes. This misleads future maintainers and creates second-system complexity: people trust docs
and build on false assumptions.  Because docs are not executed, they rot faster than code unless
explicitly tested.


## Examples (Python)

```python
def charge(amount_cents: int) -> None:
    """
    Charges the customer idempotently and will never double-charge.
    """
    payment.charge(amount_cents)  # actually not idempotent
```

```python
def get_user(user_id: str) -> dict:
    """
    Returns None if the user is not found.
    """
    raise KeyError(user_id)  # contradicts docstring
```

```python
# This function runs in O(1) time.
def find_duplicate(items):
    for i in items:
        for j in items:
            if i == j:  # actually O(n^2)
                return True
    return False
```

## Remediation

- Treat docs as part of the product: review them and keep them tied to code changes.
- Make docs executable where possible: doctests, runnable examples, or integration tests for documented behavior.
- Require ownership for security/operational docs; these claims must be verified.
- Prefer concise docs that describe intent and constraints, and avoid speculative guarantees.
