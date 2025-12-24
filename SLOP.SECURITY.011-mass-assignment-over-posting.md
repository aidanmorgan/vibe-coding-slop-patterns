# SLOP.SECURITY.011 — Mass assignment / over-posting

**Family:** SECURITY

## Summary

Mass assignment occurs when request payloads are directly applied to persistence models without
field allowlists. In Python apps, this often appears as `model(**payload)` or `setattr` loops over
incoming data. Attackers can set fields like `is_admin`, `role`, or `account_balance` if you don’t
explicitly prevent it.  AI code tends to generate “quick CRUD” endpoints that bind dicts to objects,
which is exactly where this bug lives.


## Examples (Python)

```python
def update_user(user, payload: dict):
    for k, v in payload.items():
        setattr(user, k, v)  # attacker can set is_admin=True
    user.save()
```

```python
def create_user(UserModel, payload: dict):
    return UserModel(**payload)  # no field allowlist
```

```python
def patch(model, payload: dict):
    model.__dict__.update(payload)  # bypasses invariants
```

## Remediation

- Use explicit DTOs and allowlisted fields; map only what clients are permitted to set.
- Treat privilege-related fields as server-controlled only.
- Add tests that attempt to set forbidden fields and ensure rejection.
- Prefer immutability/invariants in domain models instead of “open dict” mutation.
