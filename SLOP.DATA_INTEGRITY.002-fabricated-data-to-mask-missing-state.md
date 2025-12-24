# SLOP.DATA_INTEGRITY.002 — Fabricated data to mask missing state

**Family:** DATA_INTEGRITY

## Summary

This slop invents data instead of failing. It may auto-create records if missing, seed defaults at
runtime, or silently backfill fields. It makes demos smooth and production dishonest, leading to
phantom correctness and difficult reconciliation later.  Agents may introduce this to make tests or
UI flows “green” when wiring real dependencies is hard.


## Examples (Python)

```python
def get_user(conn, user_id: str) -> dict:
    user = conn.execute("SELECT * FROM users WHERE id=%s", (user_id,)).fetchone()
    if user is None:
        # fabrication at runtime
        conn.execute("INSERT INTO users(id, name) VALUES(%s, %s)", (user_id, "Test User"))
        return {"id": user_id, "name": "Test User"}
    return user
```

```python
def ensure_config(conn):
    cfg = conn.execute("SELECT * FROM config LIMIT 1").fetchone()
    if not cfg:
        conn.execute("INSERT INTO config(key, value) VALUES('mode', 'demo')")
```

```python
def default_profile(profile: dict | None) -> dict:
    return profile or {"theme": "sample", "bio": "lorem"}
```

## Remediation

- Never fabricate production data implicitly. Missing state should produce explicit errors or be handled by controlled migrations.
- Restrict seeding to dedicated, environment-scoped pipelines (dev/test only).
- Add monitoring for unexpected record creation patterns (e.g., sudden spikes in “default” records).
- Make defaults explicit and harmless (UI placeholders are fine), but do not persist fabricated data silently.
