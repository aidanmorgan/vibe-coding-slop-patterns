# SLOP.COMPLETENESS.001 — Shipped TODO/FIXME/HACK markers

**Family:** COMPLETENESS

## Summary

AI-assisted code often lands with “finish later” markers that look harmless in review but are
quietly dangerous in production. The problem isn’t the TODO itself; it’s where it tends to live:
right next to auth, money, validation, or persistence. Once merged, these markers become invisible
debt that can turn into security bypasses, silent corruption, or permanently “demo-mode” behavior.
Vibe/agent workflows amplify this because the tool optimizes for forward motion and plausibility.
Humans see structure, tests may still pass, and the TODO becomes a long-lived trap.


## Examples (Python)

```python
def authorize(user, action, resource):
    # TODO: integrate RBAC provider
    return True  # temporary allow-all
```

```python
def charge_customer(customer_id: str, amount_cents: int) -> None:
    # FIXME: enforce idempotency key and validate amount range
    payment_gateway.charge(customer_id, amount_cents)
```

```python
def delete_account(user_id: str) -> None:
    # HACK: skip cascading delete until we finalize data retention policy
    db.execute("DELETE FROM users WHERE id = %s", (user_id,))
```

## Remediation

- Convert free-form TODOs into tracked work immediately (issue ID + owner). If it’s not worth tracking, it’s not worth shipping.
- Treat TODOs on security boundaries (authn/authz, payments, data deletion, encryption, tenancy) as merge blockers.
- Prefer “fail closed” placeholders: raise a clear exception or return an explicit “not enabled” error instead of silently allowing unsafe behavior.
- Add a PR checklist item: “What happens if this TODO is never done?” If the answer is “bypass” or “corruption,” reject the change.
