# SLOP.COMPLETENESS.005 — Mock/sample/dummy data shipped in runtime flows

**Family:** COMPLETENESS

## Summary

This slop appears when code returns fake objects or default records to keep UI flows happy. It makes
demos smooth and production dishonest: dashboards look healthy, downstream logic develops
assumptions, and real failures get masked until customers complain.  In agent workflows, dummy data
often gets introduced as a quick “green path” fix when the agent can’t wire the real dependency
correctly.


## Examples (Python)

```python
def get_balance(user_id: str) -> dict:
    if not db.is_connected():
        return {"balance_cents": 12345}  # fake
    return db.fetch_balance(user_id)
```

```python
def list_users() -> list[dict]:
    return [{"id": "1", "name": "Test User"}]  # shipped fixture
```

```python
def fetch_profile(user_id: str) -> dict:
    profile = db.get_profile(user_id)
    return profile or {"id": user_id, "name": "Sample"}  # runtime fabrication
```

## Remediation

- Mocks belong in tests/fixtures, not runtime. If a dependency is missing, fail fast and loudly.
- If you need a demo mode, make it explicit (`DEMO_MODE=True`) and ensure it cannot be enabled in production by accident.
- Add integration tests that assert the real dependency is used (and that fake fallbacks are not).
- Monitor for “default record creation” in production; it’s often a sign of this slop escaping.
