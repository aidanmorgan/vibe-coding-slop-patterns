# SLOP.COMPLETENESS.003 — Narrative placeholders (“in a real system…”)

**Family:** COMPLETENESS

## Summary

This is the assistant openly admitting it didn’t implement the hard part—by writing a tutorial
paragraph where logic should be. It’s uniquely dangerous because it sounds responsible, and
reviewers mentally fill in the missing behavior.  In production, narrative placeholders create
security theater: a system that looks designed but behaves like a demo under adversarial or edge-
case conditions.


## Examples (Python)

```python
def get_user(user_id: str) -> dict:
    # In a real system you'd query a database and handle retries/timeouts.
    return {}  # placeholder
```

```python
def authenticate(request) -> str:
    # In production you'd verify JWT signatures against JWKS.
    return request.headers.get("X-User-Id")  # placeholder trust
```

```python
def publish_event(evt: dict) -> None:
    # A real system would enqueue this for reliable delivery.
    handle_event(evt)  # direct call as placeholder
```

## Remediation

- Treat these phrases as red flags, not documentation. If the comment describes essential behavior, the code must implement it or fail explicitly.
- Move architectural prose to docs; keep runtime code honest and complete.
- Replace “temporary trust” with “explicit not implemented” (e.g., raise or return a 501-equivalent error from your API layer).
- Require boundary implementations (authn/authz, payments, tenancy, validation) before merge—no narrative placeholders allowed.
